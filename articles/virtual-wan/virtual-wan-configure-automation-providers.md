---
title: Virtuellt WAN-nätverk i Azure-partner | Microsoft Docs
description: Den här artikeln hjälper partner ställa in Azure virtuellt WAN automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: 7f70470880845fd4271ffdbb35af771ec433babc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961951"
---
# <a name="virtual-wan-partners-preview"></a>Virtuella WAN-partner (förhandsversion)

Den här artikeln beskriver vi hur du ställer in automatiseringsmiljö för att ansluta och konfigurera en gren-enhet (en kund lokala VPN-enhet eller SDWAN CPE) för Azure virtuellt WAN-nätverk. Om du är en leverantör som tillhandahåller gren-enheter som kan hantera VPN-anslutning via IPSec-/ IKEv2 eller IPSec-/ IKEv1 är den här artikeln för dig.

En gren-enhet (kund lokala VPN-enhet eller SDWAN CPE) använder vanligtvis en styrenheten/instrumentpanel som ska etableras. SD-WAN lösning administratörer kan ofta använda en hanteringskonsol för att etablera en enhet innan den hämtar ansluten till nätverket. Den här VPN-kompatibel enhet hämtar dess plan logik från en domänkontrollant. VPN-enhet eller SD-WAN controller kan använda Azure API: er för att automatisera anslutningen till Azure virtuellt WAN-nätverk. Den här typen av anslutning kräver att den lokala enheten har en extern offentlig IP-adress som har tilldelats.

## <a name ="before"></a>Innan du börjar att automatisera

* Kontrollera att enheten har stöd för IPsec IKEv1/IKEv2. Se [standardprinciper](#default).
* Se den [REST API: er](https://docs.microsoft.com/rest/api/azure/) som du ska använda för att automatisera anslutningen till Azure virtuellt WAN-nätverk.
* Testa en Portal för Azure virtuellt WAN-nätverk.
* Sedan bestämma vilken del av anslutningen stegen som du vill automatisera. Vi rekommenderar minst automatisering:

  * Access Control
  * Överföring av gren enhetsinformation i Azure virtuellt WAN-nätverk
  * Ladda ned Azure-konfiguration och konfigurera anslutningen från gren enhet till Azure virtuellt WAN-nätverk

* Förstå den förväntade kundupplevelsen tillsammans med Azure virtuellt WAN-nätverk.

  1. Vanligtvis startar en virtuellt WAN användare processen genom att skapa en resurs som virtuellt WAN-nätverk.
  2. Du ställer in en service principal-baserad resursåtkomst för det lokala systemet (din gren domänkontrollant eller etablering programvaran för VPN-enheter) att förgreningsinformationen i Azure virtuellt WAN-nätverk.
  3. Användaren kan välja att logga in på ditt användargränssnitt och ställa in autentiseringsuppgifter för tjänstens huvudnamn. När detta är klar, din kontrollant ska kunna ladda upp informationen om gren med automation som du tillhandahåller. Manuell motsvarigheten till det på Azure-sidan är skapa webbplats.
  4. När platsinformation (gren enhet) är tillgängliga i Azure kan associerar användaren platsen till en hubb. En virtuell hubb är ett virtuellt nätverk med Microsoft-hanterad. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas en hubb per Azure-region och vpn-slutpunkten (vpngateway) i den har skapats under den här processen. VPN-gateway är en skalbar gateway storlekarna på lämpligt sätt utifrån bandbredds- och behov. Du kan välja att automatisera virtuella hubben och vpngateway skapas från instrumentpanelen gren enhetens styrenhet.
  5. När den virtuella hubben är kopplad till platsen, genereras en konfigurationsfil för användaren att manuellt hämta. Detta är om din automation kommer in och gör att användaren ska få sömlös. I stället för att användaren behöver ladda ned och konfigurera den gren-enheten manuellt, kan du ange automation och ger minimal klicka igenom upplevelse på Användargränssnittet, därmed lindra vanliga anslutningsproblem till exempel delade nyckel matchar inte, IPSec-parameter Matchningsfel, konfigurationsfilen läsbarhet osv.
  6. I slutet av det här steget i din lösning, kommer användaren ha en smidig plats-till-plats-anslutning mellan avdelningskontor enheten och virtuella hubben. Du kan också ställa in ytterligare anslutningar på andra hubs. Varje anslutning är en aktiv-aktiv-tunnel. Kunderna kan välja att använda en annan leverantör för var och en av länkarna för tunneln.

## <a name ="understand"></a>Förstå automation


###  <a name="access"></a>Åtkomstkontroll

Kunder måste kunna ställa in lämplig åtkomstkontroll för virtuellt WAN-nätverk i enhetens användargränssnitt. Detta rekommenderas använder ett huvudnamn för Azure-tjänsten. Tjänstens huvudnamn-baserad åtkomst ger enheten controller lämplig autentisering för att ladda upp informationen om grenen. Mer information finns i [skapa tjänstens huvudnamn](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Den här funktionen är utanför Azure virtuellt WAN erbjudandet, listan vi nedan de vanliga åtgärder för att konfigurera åtkomst i Azure efter vilken form av relevant information är inputted i instrumentpanelen för hantering av enheten

* Skapa ett Azure Active Directory-program för din lokala enhetens styrenhet.
* Hämta nyckel för program-ID och autentiseringsnyckel
* Hämta klientorganisations-ID
* Tilldela program till rollen ”bidragsgivare”

###  <a name="branch"></a>Överför grenen enhetsinformation

Utforma användarupplevelsen att ladda upp gren (lokala) platsinformation till Azure. [REST API: er](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) för VPNSite kan användas för att skapa platsinformationen i virtuellt WAN-nätverk. Du kan ange alla gren SDWAN/VPN-enheter eller välja enheten anpassningar efter behov.


### <a name="device"></a>Enheten nedladdning och anslutning

Det här steget omfattar att hämta Azure-konfiguration och konfigurera anslutningen från gren-enhet i Azure virtuellt WAN-nätverk. I det här steget, skulle en kund som inte använder en provider manuellt hämta Azure-konfiguration och tillämpa den på sina lokala SDWAN/VPN-enhet. Som en provider, bör du automatisera det här steget. Enhetens styrenhet kan anropa 'GetVpnConfiguration' REST API för att ladda ned Azure-konfiguration, som vanligtvis ut ungefär som följande fil.

**Konfigurationsanmärkningar**

  * Om virtuella Azure-nätverk är kopplade till den virtuella hubben, visas de som ConnectedSubnets.
  * VPN-anslutningen använder routningsbaserad konfiguration och IKEv2/IKEv1.

#### <a name="understanding-the-device-configuration-file"></a>Förstå konfigurationsfilen enhet

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
  
#### <a name="example-device-configuration-file"></a>Konfigurationsfil för exempelenhet

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

## <a name="default"></a>Standardprinciper för IPsec-anslutning

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

Din lokala SDWAN/VPN-enhet eller SD-WAN konfigurationen måste matcha eller innehålla följande algoritmer och parametrar som du anger i Azure IPsec/IKE-principen.

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN-nätverk finns i [om Azure virtuellt WAN-nätverk](virtual-wan-about.md) och [virtuellt WAN vanliga frågor om Azure](virtual-wan-faq.md).

För ytterligare information, skicka ett e- <azurevirtualwan@microsoft.com>. Ange ditt företagsnamn inom [ ] i ämnesraden.
