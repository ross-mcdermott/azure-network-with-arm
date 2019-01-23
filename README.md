# hybrid-azure-network-with-arm


override some parameters.
```
az group deployment create -g MyResourceGroup --template-file azuredeploy.json \
                            --parameters @azuredeploy.parameters.json 
                            --parameters MyValue=This MyArray=@array.json
```
