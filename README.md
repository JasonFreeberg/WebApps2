# Sample NodeJS application for GitHub Actions

Sample workflow to build and deploy node js app

### Deploy with app-level credentials

```yaml

# File: .github/workflows/workflow.yml

on: [push, pull_request]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    actions:
    
    # install dependencies, build, and test
    - name: npm install, build, and test
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
  
    - uses: ./webapp-deploy
      with: 
        app-name: node-rn
        package: './myapp'
        publish-profile-xml: '${{ secrets.azureWebAppPublishProfile }}'
      id: myapp-id
    
    # Web app url to work with
    - run: echo "Deployed the webapp at ${{ actions.myapp-id.outputs.webapp-url}}"
    
```

### Deploy with user-level credentials

```yaml

# File: .github/workflows/workflow.yml

on: [push, pull_request]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    actions:
    
    # install dependencies, build, and test
    - name: npm install, build, and test
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present

    # Login to Azure Subscription. 
    # Paste output of `az ad sp create-for-rbac` as value of secret variable: AZURE_CREDENTIALS 
    - name: Azure login
      uses: azure/login-action@master
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        
    - uses: ./webapp-deploy
      with: 
        app-name: node-rn
        package: './myapp'
      id: myapp-id
      
    # Web app url to work with
    - run: echo "Deployed the webapp at ${{ actions.myapp-id.outputs.webapp-url}}"
    
```

### Azure web app action metadata file

The action.yml file contains metadata about the Azure web app action.  

```yaml
# File: action.yml

name: 'Azure WebApp'
description: 'Deploy Web Apps to Azure'
inputs: 
  app-name: # id of input
    description: 'Name of the Azure Web App'
    required: true
    # in the future we may add 'type', for now assume string
  package: # id of input
    description: 'Path to package or folder. *.zip, *.war, *.jar or a folder to deploy'
    required: true
  publish-profile-xml: # id of input
    description: 'Publish profile (*.publishsettings) file contents with Web Deploy secrets'
    required: false
outputs:
  webapp-url: # id of output
    description: 'URL to work with your webapp'
branding:
  icon: 'webapp.svg' # vector art to display in the GitHub Marketplace
  color: 'blue' # optional, decorates the entry in the GitHub Marketplace
runs:
  using: 'node'
  main: 'main.js'
```



# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.microsoft.com.

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., label, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

# Legal Notices

Microsoft and any contributors grant you a license to the Microsoft documentation and other content
in this repository under the [Creative Commons Attribution 4.0 International Public License](https://creativecommons.org/licenses/by/4.0/legalcode),
see the [LICENSE](LICENSE) file, and grant you a license to any code in the repository under the [MIT License](https://opensource.org/licenses/MIT), see the
[LICENSE-CODE](LICENSE-CODE) file.

Microsoft, Windows, Microsoft Azure and/or other Microsoft products and services referenced in the documentation
may be either trademarks or registered trademarks of Microsoft in the United States and/or other countries.
The licenses for this project do not grant you rights to use any Microsoft names, logos, or trademarks.
Microsoft's general trademark guidelines can be found at http://go.microsoft.com/fwlink/?LinkID=254653.

Privacy information can be found at https://privacy.microsoft.com/en-us/

Microsoft and any contributors reserve all others rights, whether under their respective copyrights, patents,
or trademarks, whether by implication, estoppel or otherwise.
