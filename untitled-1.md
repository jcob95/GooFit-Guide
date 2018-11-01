# Dalitz Plot Analysis

In this example we will look at the decay of $$D^0 \rightarrow \pi^0 \pi^- \pi^+ $$and incorporate the rho meson resonances into the phase space and ultimately produce a Dalitz plot.

Firstly we need to include our libraries which we will use for GooFit

{% tabs %}
{% tab title="C++" %}
```cpp
#include <goofit/Application.h>
#include <goofit/FitManager.h>
#include <goofit/PDFs/GooPdf.h>
#include <goofit/PDFs/basic/PolynomialPdf.h>
#include <goofit/PDFs/combine/AddPdf.h>
#include <goofit/PDFs/combine/ProdPdf.h>
#include <goofit/PDFs/physics/Amp3Body.h> //3 Body Amplitude Object
#include <goofit/PDFs/physics/DalitzPlotter.h>
#include <goofit/PDFs/physics/DalitzVetoPdf.h>
#include <goofit/PDFs/physics/ResonancePdf.h> //General Resonancce Object
#include <goofit/UnbinnedDataSet.h>
#include <goofit/Variable.h>
#include <goofit/utilities/Style.h>

```
{% endtab %}

{% tab title="Python" %}
```python
from goofit import * #one line to import all goofit libraries!
import pandas as pd
import sys
import matplotlib
matplotlib.use('Agg')
import matplotlib.pyplot as plt

```
{% endtab %}
{% endtabs %}

Next  we define the masses of our particles.

{% tabs %}
{% tab title="C++" %}
```cpp
Variable fixedRhoMass("rho_mass", 0.7758, 0.01, 0.7, 0.8); //mass of rho meson we want to fit
Variable fixedRhoWidth("rho_width", 0.1503, 0.01, 0.1, 0.2); //rho meson Breit-Wigner width to fit
//masses of particles in GeV
const fptype _mD0       = 1.86484;
const fptype _mD02      = _mD0 * _mD0;
const fptype _mD02inv   = 1. / _mD02;
const fptype piPlusMass = 0.13957018;
const fptype piZeroMass = 0.1349766;

//We use these masses to make Variable objects which will used for PDF fitting
Variable motherM("motherM", _mD0);
Variable chargeM("chargeM", piPlusMass);
Variable neutrlM("neutrlM", piZeroMass);
Variable massSum("massSum", _mD0 *_mD0 + 2 * piPlusMass * piPlusMass + piZeroMass * piZeroMass); // = 3.53481                                                                                                      
Variable constantOne("constantOne", 1);
Variable constantZero("constantZero", 0);



```
{% endtab %}

{% tab title="Python" %}
```python
_mD0       = 1.86484 #masses given in GeV
_mD02      = _mD0 * _mD0
_mD02inv   = 1. / _mD02
piPlusMass = 0.13957018
piZeroMass = 0.1349766

#We still make Variable objects similar to the C++ syntax
#This is part of a function
constantOne  = Variable("constantOne", 1)
constantZero = Variable("constantZero", 0)

fixedRhoMass = Variable("rho_mass", 0.7758, 0.01, 0.7, 0.8)
fixedRhoWidth = Variable("rho_width", 0.1503, 0.01, 0.1, 0.2)


```
{% endtab %}
{% endtabs %}

For this example we will produce a Dalitz plot in the m12-m13 plane from toy data. Before fitting our resonances we first "set up" the decay.

{% tabs %}
{% tab title="First Tab" %}
```text

```
{% endtab %}

{% tab title="Second Tab" %}
```text

```
{% endtab %}
{% endtabs %}

