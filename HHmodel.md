---
jupytext:
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.15.2
kernelspec:
  display_name: Python 3
  name: python3
---

```{code-cell}
:id: dK7zScO0yTTH

!pip install interact
!pip install interactive
```

```{code-cell}
:id: k-ve-uW4j3Q6

import matplotlib.pyplot as plt
import numpy as np
import ipywidgets as widgets
from ipywidgets import interact, interactive
from IPython.display import display

def HH_model(C,Gna,Gk,Gleak):
  dt      = 0.5; # all time units in ms
  t_end = 1000;
  num_timepoints = t_end/dt;
  time    = int(num_timepoints);

  C       = 1; # (muF/cm^2)
  Gna     = 2.0; # (2mS/cm^2)
  Gk      = 1.44; # (1.44mS/cm^2)
  Gleak   = 0.2; # (0.2mS/cm^2)


  Ena     = 50.0; # (mV)
  Ek      = -77.0; # (mV)
  Eleak   = -54.0; # (mV)

  kn      = 15.0; # (mV)
  km      = 7.0; # (mV);
  tau     = 10.0; # (ms)
  Vn      = -45.0; # (mV)

  n       = np.zeros((time,1));      # this is our gating parameter
  V       = Eleak*np.ones((time,1)); # this will store our membrane potential
  Iin     = 2.0;                     # and this is an input current (muA/cm^2)

  x = np.arange(1, time, 1, dtype=int);


  for t in x:
      minf    = 1/(1+np.exp((-40.0-V[t-1])/km));
      ninf    = 1/(1+np.exp((Vn-V[t-1])/kn));
      h       = 0.89 - 1.1*n[t-1];

      dvdt = (1/C)*(-Gna*minf**3*h*(V[t-1]-Ena) - Gk*n[t-1]**4*(V[t-1]-Ek) - Gleak*(V[t-1]-Eleak) + Iin);
      dndt = (1/tau)*(ninf - n[t-1]);

      V[t] = V[t-1] + dvdt*dt;
      n[t] = n[t-1] + dndt*dt;

  x = np.arange(0, time, 1, dtype=int);

  plt.figure()
  plt.plot(x,V)
  plt.ylabel('Voltage (mV)');
  plt.xlabel('Time (ms)');
  plt.ylim(-70,);
  plt.show


interact(HH_model, C = widgets.FloatSlider(
    value=1.0,
    min=0.1,
    max=2.0,
    step=0.1,
    description='membrane capacitance:'),
    Gna = widgets.FloatSlider(
    value=2.0,
    min=0.0,
    max=5.0,
    step=0.1,
    description='Sodium conductance:'),
    Gk = widgets.FloatSlider(
    value=1.44,
    min=0.0,
    max=5.0,
    step=0.1,
    description='Potassium conductance:'),
    Gleak = widgets.FloatSlider(
    value=0.2,
    min=0.0,
    max=1.0,
    step=0.1,
    description='Leak conductance:')
)




```
