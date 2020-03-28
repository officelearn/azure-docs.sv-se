---
title: Självstudiekurs - Skapa lokalt virtuellt nätverk i Azure med Terraform
description: Självstudiekurs som illustrerar hur du implementerar ett lokalt virtuella nätverk i Azure som rymmer lokala resurser
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 361f9919fdd406a1fef6bbf2b7512dbc20266a54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159216"
---
# <a name="tutorial-create-on-premises-virtual-network-in-azure-using-terraform"></a>Självstudiekurs: Skapa lokalt virtuellt nätverk i Azure med Terraform

Den här självstudien visar hur du implementerar ett lokalt nätverk med hjälp av ett virtuellt Azure-nätverk (VNet). Ett Virtuellt Azure-nätverk kan ersättas av ditt eget privata virtuella nätverk. För att göra det, mappa lämpliga IP-adresser i undernäten.

Följande uppgifter förklaras:

> [!div class="checklist"]
> * Använd HCL (HashiCorp Language) för att implementera ett lokalt virtuella nätverk i hub-spoke-topologi
> * Använda Terraform för att skapa resurser för navnätverksinstallationer
> * Använd Terraform för att skapa lokal virtuell dator
> * Använd Terraform för att skapa lokal virtuell privat nätverksgateway

## <a name="prerequisites"></a>Krav

1. [Skapa en hubb- och ekerhybridnätverkstopologi med Terraform i Azure](./terraform-hub-spoke-introduction.md).

## <a name="create-the-directory-structure"></a>Skapa katalogstrukturen

Om du vill simulera ett lokalt nätverk skapar du ett virtuellt Azure-nätverk. Demo-VNet tar plats i ett verkligt privat lokalt nätverk. Om du vill göra samma sak med ditt befintliga lokala nätverk mappar du lämpliga IP-adresser i undernäten.

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

## <a name="declare-the-on-premises-vnet"></a>Deklarera det lokala virtuella nätverket

Skapa konfigurationsfilen för Terraform som deklarerar ett lokalt virtuella nätverk.

1. Öppna en ny fil med `on-prem.tf`namnet .

    ```bash
    code on-prem.tf
    ```

1. Klistra in följande kod i redigeringsprogrammet:

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = local.onprem-resource-group
      location = local.onprem-location
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
        allocation_method   = "Dynamic"

        tags {
            environment = local.prefix-onprem
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-onprem
        subnet_id                     = azurerm_subnet.onprem-mgmt.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.onprem-pip.id
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = azurerm_subnet.onprem-mgmt.id
      network_security_group_id = azurerm_network_security_group.onprem-nsg.id
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name   = azurerm_resource_group.onprem-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.onprem-nic.id]
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
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.onprem-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.onprem-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Spara filen och avsluta redigeringsprogrammet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett virtuellt navnätverk med Terraform i Azure](./terraform-hub-spoke-hub-network.md)