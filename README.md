
# Extensible ARM based Virtual Network Network



<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fross-mcdermott%2Fazure-network-with-arm%2Fmaster%2Fsimple-network%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/> 
</a>

<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2Fross-mcdermott%2Fazure-network-with-arm%master%2Fsimple-network%2Fazuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/> 
</a>

## Overview
The repo contains an example of using nested templates and a convention based approach to create a simple to manage and extend Virtal Network. It is designed to be expanded upon with additonal standard networking resources as a demo.

## Executing the template

The template can be executed via the above buttons, alternatively use the deployment script. For example:

```bash
.\Deploy-AzureResourceGroup.ps1 -ArtifactStagingDirectory .\sample-network -ResourceGroupLocation "Australia East" -UploadArtifacts
```

More explanation at https://rossm.tech/2019/01/an-arm-based-virtual-network-designed-for-change/

**Note:**
A limitation (see issue [17978](https://github.com/MicrosoftDocs/azure-docs/issues/17878)) with the use of references within an ARM template in combination with IF statements exists at the time of writing, which influenced the method in which the conditional logic for skipping NSG allocation to the GatewaySubnet was achived, and duplication of the NSG / UDR resource name / ID creation is required which isn't ideal. Once the issue if resolved, this can be fixed to use the output values 'nsgResourceId' and 'udrResourceId' from the nested deployment in combination with the IF statement.


