---
description: >-
  How do we produce plots after a fit. This is essentially done within your code
  and can be done with the ROOT package. Following from our fitting example,
  let's plot the results.
---

# Plotting a Fit



For this plot, we will plot our signal, background and combined distributions using histograms.

```cpp
TH1F pdfHist("pdfHist", "", xvar.getNumBins(), xvar.getLowerLimit(), xvar.getUpperLimit());
TH1F sigHist("sigHist", "", xvar.getNumBins(), xvar.getLowerLimit(), xvar.getUpperLimit());
TH1F bkgHist("bkgHist", "", xvar.getNumBins(), xvar.getLowerLimit(), xvar.getUpperLimit());
​
```

Now we have our histgrams, we will need to create our grid. For this we shall use another `UnbinnedDataset` object and fill it at the center of all the bins.

```cpp
 UnbinnedDataSet grid(xvar);
​
    for(int i = 0; i < xvar.getNumBins(); ++i) {
        double step = (xvar.getUpperLimit() - xvar.getLowerLimit()) / xvar.getNumBins();
        xvar.setValue(xvar.getLowerLimit() + (i + 0.5) * step);
        grid.addEvent();
    }
​
```

Next we need to fill our histograms with data. We must first get the signal and background probabilities for each data point as well as the complementary yields, this information is stored as a vector of vectors.

```cpp
total.setData(&grid);
    std::vector<std::vector<double>> pdfVals = total.getCompProbsAtDataPoints();
​
    TCanvas foo;
​
    double totalPdf = 0;
​
    for(int i = 0; i < grid.getNumEvents(); ++i) {
	grid.loadEvent(i);
        pdfHist.Fill(xvar.getValue(), pdfVals[0][i]);
        sigHist.Fill(xvar.getValue(), pdfVals[1][i]);
	bkgHist.Fill(xvar.getValue(), pdfVals[2][i]);
        totalPdf += pdfVals[0][i];
    }
```





