# shallot
A serverless service for publishing AWS layers.  Shallot manages packages in Poetry, then packages them as a layer published publicly to AWS.  Use this service to publish layers and make them accessible by other services.

The current form of the Poetry settings in `pyproject.toml` is set to install pandas and sagemaker.  Manual edits (described below) were used to remove numpy and scipy, so that this layer paired well with a published scipy+numpy layer (`arn:aws:lambda:us-east-1:668099181075:layer:AWSLambda-Python37-SciPy1x:2`).

[Intro to Lambda Layers in Python](https://medium.com/better-programming/intro-to-lambda-layers-in-python-95e092198c97) describes how to use a separate Serverless package to publish a layer to AWS, which you can then import in other packages.

[serverless-python-requirements](https://www.npmjs.com/package/serverless-python-requirements) contains installation instructions for the plugin, including details about Poetry support.

Poetry integration is broken somewhere, such that the `serverless-python-requirements` function for ignoring particular packages (`noDeploy`) doesn't work.  Therefore, we have to manually delete packages from the deployment `.zip` package before deploying the layer to AWS.

## Instructions
After setting up a new directory with direnv, poetry, and serverless boilerplate...

1. `poetry self:update --preview`. This brings poetry up to v1.0, which supports the `export` command.
2. `sls plugin install -n serverless-python-requirements`
3. `poetry add <all your dependencies>`
4. `poetry update`
5. `poetry install`
6. `poetry export --without-hashes -f requirements.txt > requirements.txt`. Note that this `requirements.txt` file is deleted every time you run `sls package`, so you'll have call this command frequently.
7. Make sure that Docker is running.
8. `sls package`. This creates the `.serverless` folder and package all the dependencies from `requirements.txt` into a .zip file called `pythonRequirements.zip`.
9. `cd .serverless`
10. `unzip pythonRequirements.zip`. This unzips into a folder called `python`.
11. Delete whatever packages you don't want from inside the `python` folder, including their `.dist-info` files.
11. `rm pythonRequirements.zip`
12. `zip -r pythonRequirements.zip python`
13. cd ..
14. `sls deploy --package .serverless`.  This deploys without re-packaging, using instead the .zip file you just created.

At the end of this deployment, Serverless releases the ARN of your new Lambda Layer.