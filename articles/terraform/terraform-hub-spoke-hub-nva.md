---
title: Självstudiekurs - Skapa en virtuell hubbnätverksinstallation i Azure med Terraform
description: Självstudien implementerar skapandet av Hub VNet som fungerar som en gemensam anslutningspunkt mellan alla andra nätverk
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 28ccb89d237cbe21dd0433da5f7fbb32883f6550
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159243"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-in-azure-using-terraform"></a>Självstudiekurs: Skapa en virtuell hubbnätverksinstallation i Azure med Terraform

En **VPN-enhet** är en enhet som tillhandahåller extern anslutning till ett lokalt nätverk. VPN-enheten kan vara en maskinvaruenhet eller en programvarulösning. Ett exempel på en programvarulösning är Routning och fjärråtkomsttjänst (RRAS) i Windows Server 2012. Mer information om VPN-enheter finns i [Om VPN-enheter för VPN-gateway-anslutningar från plats till plats](/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Azure stöder ett brett utbud av virtuella nätverksinstallationer att välja mellan. För den här självstudien används en Ubuntu-bild. Mer information om de många olika enhetslösningar som stöds i Azure finns på [startsidan för nätverksinstallationer](https://azure.microsoft.com/solutions/network-appliances/).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera Hub VNet i hub-spoke-topologi
> * Använd Terraform för att skapa virtuell hubbnätverksdator som fungerar som en
> * Använd Terraform för att aktivera vägar med CustomScript-tillägg
> * Använda Terraform för att skapa vägtabeller för hubb- och ekrutgateway

## <a name="prerequisites"></a>Krav

1. [Skapa en hubb- och ekerhybridnätverkstopologi med Terraform i Azure](./terraform-hub-spoke-introduction.md).
1. [Skapa lokalt virtuellt nätverk med Terraform i Azure](./terraform-hub-spoke-on-prem.md).
1. [Skapa ett virtuellt navnätverk med Terraform i Azure](./terraform-hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

1. Bläddra till [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure Cloud Shell](/azure/cloud-shell/overview). Om du inte har valt en miljö tidigare väljer du **Bash** som miljö.

    ![Cloud Shell-kommandotolk](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Ändra sökvägen till katalogen `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Ändra sökvägen till den nya katalogen:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-network-appliance"></a>Deklarera hubbnätverksverktyget

Skapa konfigurationsfilen för Terraform som deklarerar ett lokalt virtuellt nätverk.

1. Skapa en ny fil med `hub-nva.tf`namnet .

    ```bash
    code hub-nva.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = local.hub-nva-location

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub-nva
        subnet_id                     = azurerm_subnet.hub-dmz.id
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = azurerm_resource_group.hub-nva-rg.location
      resource_group_name   = azurerm_resource_group.hub-nva-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nva-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = azurerm_resource_group.hub-nva-rg.location
      resource_group_name  = azurerm_resource_group.hub-nva-rg.name
      virtual_machine_name = azurerm_virtual_machine.hub-nva-vm.name
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = azurerm_subnet.hub-gateway-subnet.id
      route_table_id = azurerm_route_table.hub-gateway-rt.id
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke1-mgmt.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke1-workload.id
      route_table_id = azurerm_route_table.spoke1-rt.id
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = azurerm_resource_group.hub-nva-rg.location
      resource_group_name           = azurerm_resource_group.hub-nva-rg.name
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = local.prefix-hub-nva
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = azurerm_subnet.spoke2-mgmt.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = azurerm_subnet.spoke2-workload.id
      route_table_id = azurerm_route_table.spoke2-rt.id
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. Spara filen och avsluta redigeringsprogrammet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett ekervirtost nätverk med Terraform i Azure](./terraform-hub-spoke-spoke-network.md)