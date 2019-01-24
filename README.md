
# Maintainable ARM based Virtual Network Network

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fross-mcdermott%2Fhybrid-azure-network-with-arm%2Fworking%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/> 
</a>

<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fross-mcdermott%2Fhybrid-azure-network-with-arm%2Fworking%2Fazuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/> 
</a>

## Executing the template

The template can be executed via the above buttons, alternatively, Azure CLI example is shown below:

```bash
az group create --name "sample-vnet-rg" --location "Australia East"

az group deployment create -g sample-vnet-rg \
        --template-uri https://raw.githubusercontent.com/ross-mcdermott/hybrid-azure-network-with-arm/working/azuredeploy.json \
        --parameters vnet-name=sample-vnet vnet-prefix=10.1
```

**Note:**
A limitation (see issue [17978](https://github.com/MicrosoftDocs/azure-docs/issues/17878))with the use of references within an ARM template in combination with IF statements exists at the time of writing, which influenced the method in which the conditional logic for skipping NSG allocation to the GatewaySubnet was achived.
