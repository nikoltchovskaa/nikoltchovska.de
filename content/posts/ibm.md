---
title: "IBM Model 1 Machine Translation in Python"
date: 2023-09-18T10:31:00+02:00
draft: false 
---

The IBM model 1 is the original gangster of statistical machine translation.
It can learn a dictionary between two languages from a number of translated sentences
without any real understanding of language and with a complete disregard for word order.

Even though, we will see that is is able to get meaningful results even from small-ish
training sets. While the usual way of explaining its working involves alignments, I find 
it much more intuitive to think about it as a variant of [Von Mises Iteration](https://en.wikipedia.org/wiki/Power_iteration).
In both cases we iteratively move some kind of mass to the most probable solution. Constant 
normalization of the solution reinforces the most probable solution while suppressing less
probable ones.

We start by asking ChatGPT for some translations:

```python
translations = [
    (
        "hallo",
        "hello",
    ),
    (
        "guten morgen",
        "good morning",
    ),
    (
        "auf wiedersehen",
        "goodbye",
    ),
    (
        "danke",
        "thank you",
    ),
    (
        "bitte",
        "please",
    ),
    (
        "ich esse gerne pizza.",
        "i enjoy eating pizza.",
    ),
    (
        "das ist ein leckeres gericht.",
        "this is a delicious dish.",
    ),
    (
        "möchtest du zum abendessen gehen?",
        "would you like to go for dinner?",
    ),
    (
        "ich trinke gerne kaffee am morgen.",
        "i like to have coffee in the morning.",
    ),
    (
        "dieses restaurant serviert köstliche pasta.",
        "this restaurant serves delicious pasta.",
    ),
    (
        "ich liebe schokoladeneis.",
        "i love chocolate ice cream.",
    ),
    (
        "kannst du mir bitte das salz reichen?",
        "can you please pass me the salt?",
    ),
    (
        "wir haben gestern abend sushi gegessen.",
        "we had sushi last night.",
    ),
    (
        "das restaurant hat eine umfangreiche speisekarte.",
        "the restaurant has an extensive menu.",
    ),
    (
        "ich mag frisches gemüse in meinem salat.",
        "i like fresh vegetables in my salad.",
    ),
    (
        "kannst du bitte das dessert bestellen?",
        "can you please order dessert?",
    ),
    (
        "das brot ist frisch aus dem ofen.",
        "the bread is fresh from the oven.",
    ),
    (
        "meine oma macht die besten pfannkuchen.",
        "my grandma makes the best pancakes.",
    ),
    (
        "wir brauchen noch einige zutaten für das abendessen.",
        "we need a few more ingredients for dinner.",
    ),
    (
        "das ist ein traditionelles deutsches gericht.",
        "this is a traditional german dish.",
    ),
    (
        "ich koche gerne italienische gerichte.",
        "i enjoy cooking italian dishes.",
    ),
    (
        "der kellner brachte uns die speisekarte.",
        "the waiter brought us the menu.",
    ),
    (
        "ich habe eine allergie gegen erdnüsse.",
        "i have an allergy to peanuts.",
    ),
    (
        "heute abend machen wir grillen im garten.",
        "tonight, we're having a barbecue in the garden.",
    ),
    (
        "ich probiere gerne exotische gerichte.",
        "i enjoy trying exotic dishes.",
    ),
    (
        "die suppe ist noch heiß.",
        "the soup is still hot.",
    ),
    (
        "ich habe lust auf ein stück schokoladenkuchen.",
        "i'm in the mood for a piece of chocolate cake.",
    ),
    (
        "die kellnerin empfahl den fisch des tages.",
        "the waitress recommended the fish of the day.",
    ),
    (
        "wir haben gestern ein neues restaurant ausprobiert.",
        "we tried a new restaurant yesterday.",
    ),
    (
        "die pizza ist mit peperoni und oliven belegt.",
        "the pizza is topped with pepperoni and olives.",
    ),
    (
        "ich esse lieber sushi mit stäbchen.",
        "i prefer eating sushi with chopsticks.",
    ),
    (
        "das restaurant serviert authentische mexikanische tacos.",
        "the restaurant serves authentic mexican tacos.",
    ),
    (
        "möchtest du etwas zum knabbern während des films?",
        "would you like something to snack on during the movie?",
    ),
    (
        "ich habe gestern abend spaghetti bolognese gekocht.",
        "i cooked spaghetti bolognese last night.",
    ),
    (
        "das brot schmeckt besonders gut mit butter.",
        "the bread tastes especially good with butter.",
    ),
    (
        "das restaurant hat eine tolle auswahl an vorspeisen.",
        "the restaurant has a great selection of appetizers.",
    ),
    (
        "in der bäckerei gibt es frische croissants.",
        "there are fresh croissants available at the bakery.",
    ),
    (
        "die suppe ist gut gewürzt und heiß.",
        "the soup is well-seasoned and hot.",
    ),
    (
        "ich habe einen tisch für zwei personen reserviert.",
        "i've reserved a table for two people.",
    ),
    (
        "heute abend bestellen wir chinesisches essen.",
        "tonight, we're ordering chinese food.",
    ),
    (
        "kannst du mir bitte das salatdressing reichen?",
        "can you please pass me the salad dressing?",
    ),
    (
        "ich habe eine vorliebe für scharfe gerichte.",
        "i have a preference for spicy dishes.",
    ),
    (
        "das restaurant bietet vegetarische optionen an.",
        "the restaurant offers vegetarian options.",
    ),
    (
        "die kuchen in dieser bäckerei sind unwiderstehlich.",
        "the cakes at this bakery are irresistible.",
    ),
    (
        "wir planen eine grillparty am samstag.",
        "we're planning a barbecue party on saturday.",
    ),
]

# do some simple normalization and tokenization
def pre(s: str) -> list[str]:
    return s.replace(",", " ").replace(".", "").replace("?", "").lower().split()

# we also swap the tuples so e=english and f=foreign (as is customary for IBM models)
translations = [(pre(e), pre(d)) for d, e in translations]
```

The implementation follows the pseudocode of [Philipp Koehns Slides](https://www2.statmt.org/book/)

```python
from collections import defaultdict
t = defaultdict(lambda: defaultdict(lambda: 0.1))

for _ in range(100):
    count = defaultdict(lambda: defaultdict(float))
    total = defaultdict(float)

    all_words_e = set(word for sentence, _ in translations for word in sentence)
    all_words_f = set(word for _, sentence in translations for word in sentence)
    s_total = defaultdict(float)

    for e, f in translations:

        # calculate how much probability mass each english word will recieve
        for we in e:
            for wf in f:
                s_total[we] += t[we][wf]

        # collect
        for we in e:
            for wf in f:
                # give each english word its probability mass
                count[we][wf] += t[we][wf] / s_total[we]

                # keep track how much probability mass each foreign word gave away
                total[wf] += t[we][wf] / s_total[we]

    # estimate probabilities
    for wf in all_words_f:
        for we in all_words_e:
            # normalize t(e|f), so that \sum_f t(e|f) = 1 for all e, ie we get proper probability distributions.
            t[we][wf] = count[we][wf] / total[wf]

```

Finally, dump our dictionary:

```python
words_f = set()
words_e = set()

for e, f in translations:
    for we in e:
        words_e.add(we)
    for wf in f:
        words_f.add(wf)

for we in words_e:
    prob, wf = max((t[we][wf], wf) for wf in words_f)
    if prob < 0.9:
        # skip unconverged words
        continue
    print(f"{we} -> {wf} \t ({prob:.2f})")
```

The result is pretty impressive, the overwhelming majority of translations are solid:

```
extensive -> umfangreiche 	 (0.99)
is -> ist 	 (1.00)
hello -> hallo 	 (1.00)
good -> guten 	 (0.99)
and -> und 	 (1.00)
can -> kannst 	 (1.00)
had -> gegessen 	 (0.99)
dressing -> salatdressing 	 (1.00)
pizza -> pizza 	 (1.00)
eating -> esse 	 (1.00)
serves -> serviert 	 (1.00)
goodbye -> wiedersehen 	 (1.00)
sushi -> sushi 	 (1.00)
please -> bitte 	 (1.00)
dishes -> gerichte 	 (1.00)
an -> eine 	 (0.99)
bread -> brot 	 (1.00)
enjoy -> gerne 	 (0.94)
has -> hat 	 (1.00)
well-seasoned -> gewürzt 	 (1.00)
restaurant -> restaurant 	 (1.00)
menu -> speisekarte 	 (1.00)
salt -> salz 	 (0.99)
with -> mit 	 (1.00)
morning -> morgen 	 (1.00)
```
