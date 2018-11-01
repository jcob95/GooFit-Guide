# A Basic Fit WIth GooFit

In this example we are going to look at fitting a Gaussian signal with a polynomial background just as you might be familiar with in RooFit. This example can be found in the build directory under Addition. We will look at the source file addition.cpp

First let us setup the variables which we will need.

```cpp
GooFit::Application app("Addition example", argc, argv);
​
    GOOFIT_PARSE(app);
​
    //some fancy styling for our plot
    gStyle->SetCanvasBorderMode(0);
    gStyle->SetCanvasColor(10);
    gStyle->SetFrameFillColor(10);
    gStyle->SetFrameBorderMode(0);
    gStyle->SetPadColor(0);
    gStyle->SetTitleColor(1);
    gStyle->SetStatColor(0);
    gStyle->SetFillColor(0);
    gStyle->SetFuncWidth(1);
    gStyle->SetLineWidth(1);
    gStyle->SetLineColor(1);
    gStyle->SetPalette(1, 0);
    
    //our variable which we are fitting
    Observable xvar{"xvar", -5, 5};
    //The dataset
    UnbinnedDataSet data(xvar);
    //an ordinary root histogram
    TH1F xvarHist("xvarHist", "", xvar.getNumBins(), xvar.getLowerLimit(), xvar.getUpperLimit());
​
    xvarHist.SetStats(false);
​
```

Now you may notice the obvious lines

```cpp
GooFit::Application app("Addition example", argc, argv);
​
    GOOFIT_PARSE(app);
```

These are necessary to run any GooFit program. You don't need to worry too much about what it does. It simply offers a way to provide extra functionality such as choosing what \(GPU\) device to run GooFit on.

Ignoring all the familiar ROOT things, we come to something which may seem familiar from RooFit

```cpp
    Observable xvar{"xvar", -5, 5};
    //The dataset
    UnbinnedDataSet data(xvar);
```

First we have an `Observable` which is similar to `RooRealVar` and simply represents the variable which we are fitting. We also have `UnbinnedDataSet`which is `GooFit` class for storing well unbinned data. In this case we are storing xvar data in an instance called `data`.

OK, now let's get some toy data. In this example we are going to produce a Gaussian signal with a uniform background and add it to our data using the `addEvent` function. Note: if you wanted to changed the what ROOT random generator to use \(e.g. TRandom or TRandom3\) it is simply case of just adding the right ROOT header file.

```cpp
  TRandom3 donram(42);
    double totalData = 0;
​
    for(int i = 0; i < 100000; ++i) {
        xvar.setValue(donram.Gaus(0.2, 1.1));
​
        if(donram.Uniform() < 0.1)
            xvar.setValue(donram.Uniform(xvar.getLowerLimit(), xvar.getUpperLimit()));
​
        if(fabs(xvar.getValue()) > 5) {
            --i;
            continue;
        }
        //fill our UnbinnedDataSet here 
        data.addEvent();
        xvarHist.Fill(xvar.getValue());
        totalData++;
    }
​
```

### 

### Fit parameters and PDFs <a id="fit-parameters-and-pdfs"></a>

Now we have our data, lets build our PDFs. Once again we have some objects which might be familiar from RooFit.

```cpp
Variable xmean{"xmean", 0, 1, -10, 10};
    Variable xsigm{"xsigm", 1, 0.5, 1.5};
    GaussianPdf signal{"signal", xvar, xmean, xsigm};
​
    Variable constant{"constant", 1.0};
    PolynomialPdf backgr{"backgr", xvar, {constant}};
    
    //the weighting of our PDF components
    Variable sigFrac{"sigFrac", 0.9, 0.75, 1.00};
​
    AddPdf total{"total", {sigFrac}, {&signal, &backgr}};
​
```

Our fitting parameters are defined using the `Variable` class. looking at the sigma of our soon to be Gaussian Function, `xsigm` , we first define a string with the name of the variable, then it's initial starting value and the range it is constrained to . There is an additional option a variable may have and that is an error scale which is an additional parameter as seen with `xmean` . The constructor with this option is then `Variable("name",starting value, error scale, minimum, maximum)`.

Once we have our background and signal parameterisation we are ready to build our model. We do this by using the `AddPdf` function which takes in our PDF weights and functions, note the syntax. The final step to performing our fit is the following.

```cpp
    total.setData(&data);
    FitManager fitter(&total);
    fitter.fit();
​
```

we first ensure our dataset is assigned to our PDF model using `setData` and then we construct our `FitManager` which as the name suggests, takes care of the fitting process for us. Plotting our fit results is not as straight forward. This is explained in the next section.

