# shallot
A serverless application for publishing AWS layers

[Intro to Lambda Layers in Python](https://medium.com/better-programming/intro-to-lambda-layers-in-python-95e092198c97) describes how to use a separate Serverless package to publish a layer to AWS, which you can then import in other packages.

[serverless-python-requirements](https://www.npmjs.com/package/serverless-python-requirements) contains installation instructions for the plugin, including details about Poetry support.

## Instructions
1. `poetry self:update --preview`. This brings poetry up to v1.0, which supports exporting dependencies to `requirements.txt`
2. `sls plugin install -n serverless-python-requirements`
3. `poetry add <all your dependencies>`
4. `poetry update`
5. `poetry install`
6. `poetry export --without-hashes -f requirements.txt > requirements.txt`
7. Make sure that Docker is running
8. `sls deploy`
