---
title: "How to debug NaN Values in PyTorch Models during Training"
date: 2023-01-13T10:31:00+02:00
draft: false 
summary: "It's really slow."
---


```python
# FROM THE DEPTH OF THE POND SHINES THE TORCH OF LIGHT WHERE THERE
# IS GREED IN MEN AND LIGHT IN THE STONE WHERE THE ANCIENT SPIRIT
# LIVES THAT CONSUMES TIME TO SHED TRUTH UPON NUMBERS THAT EXCEED THE
# REACH OF GOD IT REQUIRES THE ULTIMATE SACRIFICE OF TIME OH HOW SLOW
# IT IS BUT ONLY IT TRULY IS POWERFUL ENOUGH TO REDEEM MY ASSERTS AND
# TO FREE OUR PITYFUL HUMAN SPIRITS FROM THOSE STUPID NANS IN MY MODEL
# PARAMETERS PLS
torch.autograd.set_detect_anomaly(True)
```

It's really slow. ([via](https://github.com/pytorch/pytorch/issues/1274))
