---
title: "Pigeons"
date: 2022-04-16T12:05:06+02:00
draft: false 
---

TL;DR: I built a wifi into an electric water gun to shoot the pigeons off my balcony, controlled over the internet by a python script running openCV reading the camera image of my old iPhone.

The pigeons in my backyard find particular pleasures in voiding their excrements onto my balcony. Dissatisfied with this situation, I went online to find a solution. I created a handy table to give you an overview of the vast number of ~effective~ ways to get rid if pigeons:

| Approach | Why it doesn't work |
|:--|:--|
| Plastic crows | Pigeons get used to them |
| Crow stickers | Pigeons get used to them |
| Reflective wind mills / CDs / ... | Pigeons get used to them |
| Sounds of animals (dogs, predators) | Pigeons get used to them |
| Ultrasonic sounds | Hear me out: Even the people advocating for it admit that pigeons (like humans) are incapable of perceiving ultrasonic sounds. Instead they think that pigeons are [wavies](https://en.wikipedia.org/wiki/Electromagnetic_hypersensitivity) and that the mere presence of some imperceptible waves somehow distress the pigeons. You can't make this stuff up. Of cause nobody ever had any success with it. |
| Dog and cat hair | Pigeons get used to them |
| Shooting pigeons | While it would certainly help coping with the distress of cleaning their crap, its highly illegal in Germany and even after one of my neighbours apparently tried it (I just saw the police leaflets) the number of pigeons didn't noticeably decrease. To be fair, some random neighbour with an air rifle is probably no comparison to the olympic pigeon killers [of the past](https://en.wikipedia.org/wiki/Shooting_at_the_1900_Summer_Olympics#Excluded_events).|
| Getting a cat | That should actually work if the cat has regular access to the balcony. But I don't have a cat and it would probably suicide itself in its attempt to hunt pigeons in the fifth floor. |
| Spikes | They work IFF you buy the right ones AND put them absolutely everywhere AND are careful to actually mount them correctly AND you don't plan to use the area covered yourself at all AND you don't even want to look at its disgraced looks. So it is completely pointless for a balcony.
| Nets | They work if you can tolerate their looks (I don't) and mount them correctly. On the other side, they are often times not mounted correctly in which case you have to remove dead or alive pigeons on a somewhat regular basis. |
| Scaring them away by shouting, water gun, clapping, ... | Pigeons get used to it. Also you typically spend way too little time on the balcony to do keep them away permanently. But what if I outsourced the water gunning to some robotic contraption?|



After a quick burst of manic energy during exam phase, I decided to solve the pigeon problem on my balcony once and for all (and in a humane way). The idea was quite simple: Mount an electric water gun on my balcony, detect pigeons with a web cam and shoot them. Pigeons should not get used to getting sprayed at (at least not while its coldish), I don't have to disfigure my balcony and I'm not harming the pigeons in any way or even risk killing them. As I approached this project during my exam phase, it should also be quick to implement using things I already have  laying around.

## The water gun

I bought a cheap electric water gun [from Amazon](https://www.amazon.de/Wasserpistole-Elektrische-Wasserblaster-Wasserdicht-Wasserspielzeug/dp/B09578C69R/ref=sr_1_6?keywords=wasserpistole+elektrisch&qid=1650101643&sprefix=wasserpistole+ele%2Caps%2C147&sr=8-6) and tested it out: The range of around 3-4 meters is pathetic, but plenty for my balcony.

Next, I opened it up to figure out how to control it over the internet:



; 3D printed a window mount for my old iPhone (running a [webcam app](https://apps.apple.com/de/app/ipcamera-high-end-networkcam/id570912928?l=en)) and hacked together some openCV code in python.
After all this was working on my desk, I had the pleasure to discover that the PCB Antenna of the ESP is too weak to receive WiFi on my balcony so I connected the ESP to my (current) iPhone hotspot and wrote a small go relay for my server so that my laptop can send the shooting commands to the water gun *over the internet*. Oh and while I was at it, I added a Siri Shortcut, so it’s voice controllable, too. (They are surprisingly hacker-friendly)
The surprising thing is, that this Ruben Goldberg machine actually works really well and without much fiddling.
