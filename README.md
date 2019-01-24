# hybrid-azure-network-with-arm

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-AAD-DomainServices%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/> 
</a>

<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-AAD-DomainServices%2Fazuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/> 
</a>


override some parameters.
```
az group deployment create -g MyResourceGroup --template-file azuredeploy.json \
                            --parameters @azuredeploy.parameters.json 
                            --parameters MyValue=This MyArray=@array.json
```


{
            "condition": "[equals(parameters('enable-hybrid'),'true')]",
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Network/localNetworkGateways",
            "name": "[variables('vnet-local-gateway-name')]",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "Local Network Gateway (to Office)"
            },
            "dependsOn": [
                "[parameters('vnet-name')]"
            ],
            "properties": {
                "localNetworkAddressSpace": {
                    "addressPrefixes": "[parameters('vpn-local-address-prefixes')]"
                },
                "gatewayIpAddress": "[parameters('vpn-local-gateway-ip-address')]"
            }
        },
        {
            "condition": "[equals(parameters('enable-hybrid'),'true')]",
            "apiVersion": "2016-03-30",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('vnet-gateway-ip-name')]",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "Virtual Network Gateway Public IP Address"
            },
            "dependsOn": [
                "[parameters('vnet-name')]"
            ],
            "properties": {
                "publicIPAllocationMethod": "Dynamic"
            }
        },
        {
            "condition": "[equals(parameters('enable-hybrid'),'true')]",
            "apiVersion": "2017-06-01",
            "name": "[variables('vnet-gateway-name')]",
            "type": "Microsoft.Network/virtualNetworkGateways",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "Virtual Network Gateway"
            },
            "dependsOn": [
                "[variables('vnet-gateway-ip-name')]",
                "[parameters('vnet-name')]"
            ],
            "properties": {
                "gatewayType": "Vpn",
                "ipConfigurations": [
                    {
                        "name": "default",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[concat(resourceId('Microsoft.Network/virtualNetworks',parameters('vnet-name')),'/subnets/GatewaySubnet')]"
                            },
                            "publicIpAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('vnet-gateway-ip-name'))]"
                            }
                        }
                    }
                ],
                "enableBgp": false,
                "vpnType": "RouteBased",
                "sku": {
                    "name": "[parameters('vpn-sku')]",
                    "tier": "[parameters('vpn-sku')]"
                }
            }
        },
        {
            "condition": "[equals(parameters('enable-hybrid'),'true')]",
            "apiVersion": "2015-06-15",
            "name": "[variables('vnet-to-dc-connection-name')]",
            "type": "Microsoft.Network/connections",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "VNG to LNG Connection"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnet-name'))]",
                "[concat('Microsoft.Network/virtualNetworkGateways/', variables('vnet-gateway-name'))]",
                "[concat('Microsoft.Network/localNetworkGateways/', variables('vnet-local-gateway-name'))]"
            ],
            "properties": {
                "virtualNetworkGateway1": {
                    "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('vnet-gateway-name'))]"
                },
                "localNetworkGateway2": {
                    "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('vnet-local-gateway-name'))]"
                },
                "connectionType": "IPsec",
                "routingWeight": 10,
                "sharedKey": "[parameters('vpn-shared-key')]"
            }
        }

          "local-dc-address-prefix": {
            "type": "string",
            "metadata": {
                "description": "Internal address prefix of the DC for routing purposes"
            }
        },
        "enable-hybrid": {
            "type": "boolean",
            "metadata": {
                "description": "True to include the VPN connectivity"
            },
            "defaultValue": "false"
        },
        "vpn-sku": {
            "type": "string",
            "metadata": {
                "description": "VPN SKU to use"
            },
            "defaultValue": "Standard"
        },
        "vpn-shared-key": {
            "type": "securestring",
            "metadata": {
                "description": "Shared key to initiate the connection to the local site (DC). Use KeyVault in production scenarios."
            },
            "defaultValue": "NA"
        }