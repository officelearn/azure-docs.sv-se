---
title: Konfigurera Azure virtuellt WAN automation - för virtuella WAN-partner | Microsoft Docs
description: Den här artikeln hjälper programdefinierade lösningar anslutningspartners ställa in Azure virtuellt WAN automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918908"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Konfigurera virtuellt WAN-nätverk automation - för partner virtuellt WAN-nätverk (förhandsversion)

Den här artikeln beskriver vi hur du ställer in automatiseringsmiljö för att ansluta och konfigurera en gren-enhet (en kund lokala VPN-enhet eller SDWAN) för Azure virtuellt WAN-nätverk. Om du är en leverantör som tillhandahåller gren-enheter som kan hantera VPN-anslutning via IPsec/IKEv2, är den här artikeln för dig.

Programvarudefinierad anslutning lösningarna använder vanligtvis en domänkontrollant eller en device provisioning-center för att hantera deras gren-enheter. Kontrollanten kan använda Azure API: er för att automatisera anslutningen till Azure virtuellt WAN-nätverk. Den här typen av anslutning kräver en SDWAN eller VPN-enheten på plats som har en externt riktad mot offentliga IP-adress tilldelad till den.

##  <a name="access"></a>Åtkomstkontroll

Kunder måste kunna ställa in lämplig åtkomstkontroll för virtuellt WAN-nätverk i enhetens användargränssnitt. Detta rekommenderas använder ett huvudnamn för Azure-tjänsten. Tjänstens huvudnamn-baserad åtkomst ger enheten controller lämplig autentisering för att ladda upp informationen om grenen. Mer information finns i [skapa tjänstens huvudnamn](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Ladda upp informationen om gren

Utforma användarupplevelsen att ladda upp gren (lokala) platsinformation till Azure. [REST API: erna](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) för **VPNSite** kan användas för att skapa platsinformationen i virtuellt WAN-nätverk. Du kan ange alla gren SDWAN/VPN-enheter eller välja enheten anpassningar efter behov.

##  <a name="hub"></a>Hub och tjänster

När enheten grenen har överförts till Azure, kunden vanligtvis att göra val av hubbregion och/eller tjänster i Azure-portalen, som anropar en uppsättning åtgärder för att skapa det virtuella navnätverket och VPN-slutpunkt i hubben. VPN-gateway är en skalbar gateway storlekarna på lämpligt sätt utifrån bandbredds- och behov.

## <a name="device"></a>Enhetskonfiguration

I det här steget, skulle en kund som inte använder en provider manuellt hämta Azure-konfiguration och tillämpa den på sina lokala SDWAN/VPN-enhet. Som en provider, bör du automatisera det här steget. Kontrollanten kan anropa **GetVpnConfiguration** REST API för att ladda ned Azure-konfiguration, som vanligtvis ut ungefär som följande fil.

**Konfigurationsanmärkningar**

  * Om virtuella Azure-nätverk är kopplade till den virtuella hubben, visas de som ConnectedSubnets.
  * VPN-anslutningen använder routningsbaserad konfiguration och IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Förstå konfigurationsfilen enhet

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections** – Det här avsnittet innehåller information om följande:

    * **Adressutrymme** av de virtuella NAV VNet.<br>Exempel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adressutrymme** av de virtuella nätverk som är anslutna till hubben.<br>Exempel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning för vpngateway består av två tunnlar i aktiv-aktiv konfiguration ser du båda IP-adresserna listade i den här filen. I det här exemplet ser du "Instance0" och "Instance1" för varje plats.<br>Exempel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Konfigurationsinformation för vpngatewayanslutning**, exempelvis BGP, i förväg delad nyckel osv. PSK är den i förväg delade nyckeln som genereras automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
### <a name="example-device-configuration-file"></a>Konfigurationsfil för exempelenhet

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="default"></a>Standardprinciper

### <a name="initiator"></a>Initierare

Följande avsnitt listar stöds princip kombinationerna när Azure är initierare för tunneln.

**Fas 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fas 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Svarare

Följande avsnitt listar kombinationerna stöds principen när Azure är svarande för tunneln.

**Fas 1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**Fas 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Behöver allt matcha mellan den virtuella hubb vpngateway principen och Mina lokala SDWAN/VPN-enhet eller SD-WAN konfiguration?

Din lokala SDWAN/VPN-enhet eller SD-WAN konfigurationen måste matcha eller innehålla följande algoritmer och parametrar som du anger i Azure IPsec/IKE-principen. SA-livstider är lokala specifikationer och behöver inte matcha.

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp

## <a name="feedback"></a>Feedback för förhandsversionen

Vi vill gärna ha din feedback. Skicka ett e-postmeddelande till <azurevirtualwan@microsoft.com> om du vill rapportera problem eller lämna feedback (positiv och negativ) om Virtual WAN. Ange ditt företagsnamn inom [ ] i ämnesraden. Ange även ditt prenumerations-ID om du rapporterar ett problem.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN-nätverk finns i [om Azure virtuellt WAN-nätverk](virtual-wan-about.md) och [virtuellt WAN vanliga frågor om Azure](virtual-wan-faq.md).
