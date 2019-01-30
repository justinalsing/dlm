# Contributing

Here I provide a short summary of how to contribute extended DLM models to this package. 

Note that in order to contribute new models, you will need to be able to write code in [stan](https://mc-stan.org) (click on the link for documentation and access to the stan manual). This might sound like pain for the uninitiated, but stan is a very intuitive language and you will only need to make relatively minor changes to the existing models code in order to implement new models (see below).

## Conding extended models

The suite of DLM models implemented here are completely characterized by the matrices **F**, **G**, **W** and **C** (described in the file `model_descriptions.pdf`). In order to develop a new DLM model within dlmmc, more-or-less all you need to do is work out what the matrices of your model are, copy one of the existing stan models in `models/stan_dlm_models.py` and replace the matrices with your own needs. You'll then need to add some lines to `compile_stan_models.py` (copy-paste from the other examples) and re-run `compile_stan_models.py` to compile your new model.

A more detailed work-flow for implementing a new model is as follows:

1. **Copy-paste one of the existing stan models** inside `stan_dlm_models.py` and re-name it.
2. **Update the `data{}` block** to add any additional data / quantities that are required to build your model matrices.
3. **Update the `transformed_data{}` block**, replacing the model matrix **F** construction with your own requirements and making sure the length of the state vector `nx` is correct for your new model.
4. **Update the `transformed_parameters{}` block** replacing the model matrices **G**, **W** and **C** constructions with your derived model matrices.
5. **Update the `generated_quantities{}` block** to extract the new bits of your model from the state vector. At the end of this block of code (the last loop in each stan model code), there is a loop which takes the state vector **x** and extracts from it the various components of your model (trend, seasonal cycle, etc). This is useful to make post-processing the output easier. In your new model, you should add lines of code here (following what was done for the other components of the state vector) to extract the various components of your model from **x**.

Once you think you're done coding your new model, you'll need to compile it. This can be done with the following python code:

`import pystan`
`from models.stan_dlm_models import *`

`my_new_model = pystan.StanModel(model_code=my_new_model)`

If it compiles, go ahead and run it on your data following `dlm_tutorial.ipynb`.
