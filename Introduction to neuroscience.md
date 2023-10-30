---
jupytext:
  cell_metadata_filter: -all
  formats: md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.5
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Notebooks with MyST Markdown

Jupyter Book also lets you write text-based notebooks using MyST Markdown.
See [the Notebooks with MyST Markdown documentation](https://jupyterbook.org/file-types/myst-notebooks.html) for more detailed instructions.
This page shows off a notebook written in MyST Markdown.

## An example cell

With MyST Markdown, you can define code cells with a directive like so:

```{code-cell}
{
    "tags": [
        "hide-input"
    ]
}

# a conductance-based example of the reduced hodgekin-huxley model
!pip install interact --quiet
!pip install interactive --quiet

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
  Gna     = 2; # (2mS/cm^2) 
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

  plt.figure(1)
  plt.plot(x,V)
  plt.ylabel('Voltage (mV)');
  plt.xlabel('Time (ms)');
  plt.ylim(-70,20);
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


```{code-cell}
from IPython.display import HTML
# Youtube
HTML('<iframe width="560" height="315" src="https://www.youtube.com/embed/74YrCK9ER5c?si=1xnPLqoLH4kQkPG9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>')
```

When your book is built, the contents of any `{code-cell}` blocks will be
executed with your default Jupyter kernel, and their outputs will be displayed
in-line with the rest of your content.

```{seealso}
Jupyter Book uses [Jupytext](https://jupytext.readthedocs.io/en/latest/) to convert text-based files to notebooks, and can support [many other text-based notebook files](https://jupyterbook.org/file-types/jupytext.html).
```

## Create a notebook with MyST Markdown

MyST Markdown notebooks are defined by two things:

1. YAML metadata that is needed to understand if / how it should convert text files to notebooks (including information about the kernel needed).
   See the YAML at the top of this page for example.
2. The presence of `{code-cell}` directives, which will be executed with your book.

That's all that is needed to get started!

## Quickly add YAML metadata for MyST Notebooks

If you have a markdown file and you'd like to quickly add YAML metadata to it, so that Jupyter Book will treat it as a MyST Markdown Notebook, run the following command:

```
jupyter-book myst init path/to/markdownfile.md
```
