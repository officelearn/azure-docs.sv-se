---
title: Automatiseringsriktlinjer för Azure Virtual WAN-partner | Microsoft-dokument
description: Den här artikeln hjälper partner att konfigurera Azure Virtual WAN-automatisering.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190421"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Riktlinjer för automatisering för Virtual WAN-partner

Den här artikeln hjälper dig att förstå hur du konfigurerar automatiseringsmiljön för att ansluta och konfigurera en filialenhet (en kund på lokal VPN-enhet eller SDWAN CPE) för Azure Virtual WAN. Om du är en leverantör som tillhandahåller filialenheter som kan hantera VPN-anslutning via IPsec/IKEv2 eller IPsec/IKEv1, är den här artikeln för dig.

En filialenhet (en lokal VPN-enhet för kunder eller SDWAN CPE) använder vanligtvis en instrumentpanel för styrenhet/enhet som ska etableras. SD-WAN-lösningsadministratörer kan ofta använda en hanteringskonsol för att företablering av en enhet innan den ansluts till nätverket. Denna VPN-kompatibla enhet får sin styrplanslogik från en styrenhet. VPN-enheten eller SD-WAN-styrenheten kan använda Azure API:er för att automatisera anslutningen till Azure Virtual WAN. Den här typen av anslutning kräver att den lokala enheten har en externt riktad offentlig IP-adress tilldelad den.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Innan du börjar automatisera

* Kontrollera att enheten stöder IPsec IKEv1/IKEv2. Se [standardprinciper](#default).
* Visa [REST-API:erna](#additional) som du använder för att automatisera anslutningen till Azure Virtual WAN.
* Testa portalupplevelsen för Azure Virtual WAN.
* Bestäm sedan vilken del av anslutningsstegen du vill automatisera. Vi rekommenderar åtminstone att du automatiserar:

  * Åtkomstkontroll
  * Överföring av information om filialenheter till Azure Virtual WAN
  * Hämta Azure-konfiguration och konfigurera anslutning från filialenhet till Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Ytterligare information

* [REST API](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) för att automatisera skapandet av virtuella hubben
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) för att automatisera Azure VPN-gateway för Virtuell WAN
* [REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) för att ansluta en VPNSite till en Azure VPN Hub
* [Standardprinciper för IPsec](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Kundupplevelse

Förstå den förväntade kundupplevelsen tillsammans med Azure Virtual WAN.

  1. Vanligtvis startar en virtuell WAN-användare processen genom att skapa en virtuell WAN-resurs.
  2. Användaren ställer in en tjänsthuvudbaserad resursgruppsåtkomst för det lokala systemet (din filialkontrollant eller VPN-enhetsetableringsprogram) för att skriva filialinformation till Azure Virtual WAN.
  3. Användaren kan för närvarande besluta att logga in på användargränssnittet och ställa in tjänstens huvudautentiseringsuppgifter. När det är klart bör din handkontroll kunna ladda upp filialinformation med den automatisering du ska tillhandahålla. Den manuella motsvarigheten till detta på Azure-sidan är "Skapa plats".
  4. När information om platsen (filialenheten) är tillgänglig i Azure ansluter användaren webbplatsen till ett nav. Ett virtuellt nav är ett microsofthanteradt virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Navet är kärnan i ditt nätverk i en region. Det kan bara finnas en hubb per Azure-region och vpn-slutpunkten (vpngateway) inuti den skapas under den här processen. VPN-gatewayen är en skalbar gateway som storlekar på lämpligt sätt baserat på bandbredd och anslutningsbehov. Du kan välja att automatisera virtuella nav och vpngateway skapande från din gren enhet controller instrumentpanelen.
  5. När den virtuella hubben är associerad till platsen genereras en konfigurationsfil som användaren kan hämta manuellt. Det är här din automatisering kommer in och gör användarupplevelsen sömlös. I stället för att användaren manuellt måste hämta och konfigurera filialenheten kan du ställa in automatiseringen och ge minimal klickupplevelse på användargränssnittet, vilket lindrar typiska anslutningsproblem som delad nyckelmatchning, IPSec-parameter matchning, konfigurationsfil läsbarhet etc.
  6. I slutet av det här steget i din lösning har användaren en sömlös anslutning från plats till plats mellan grenenheten och det virtuella navet. Du kan också ställa in ytterligare anslutningar över andra hubbar. Varje anslutning är en aktiv-aktiv tunnel. Kunden kan välja att använda en annan Internetleverantör för var och en av länkarna för tunneln.
  7. Överväg att tillhandahålla felsöknings- och övervakningsfunktioner i CPE-hanteringsgränssnittet. Typiska scenarier är "Kunden kan inte komma åt Azure-resurser på grund av ett CPE-problem", "Visa IPsec-parametrar på CPE-sidan" etc.

## <a name="automation-details"></a><a name ="understand"></a>Information om automatisering

###  <a name="access-control"></a><a name="access"></a>Åtkomstkontroll

Kunder måste kunna ställa in lämplig åtkomstkontroll för Virtuellt WAN i enhetsgränssnittet. Detta rekommenderas att använda ett Azure Service Principal.This is recommended using an Azure Service Principal. Tjänstens huvudbaserade åtkomst ger enhetsstyrenheten lämplig autentisering för att ladda upp filialinformation. Mer information finns i [Skapa tjänstens huvudnamn](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Även om den här funktionen ligger utanför Azure Virtual WAN-erbjudandet, listar vi nedan de typiska stegen för att konfigurera åtkomst i Azure varefter relevant information matas in i instrumentpanelen för enhetshantering

* Skapa ett Azure Active Directory-program för din lokala enhetsstyrenhet.
* Hämta program-ID och autentiseringsnyckel
* Hämta klientorganisations-ID
* Tilldela programmet till rollen "Deltagare"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Ladda upp information om filialenheter

Du bör utforma användarupplevelsen för att ladda upp branchinformation (lokal webbplats) till Azure. Du kan använda [REST API:er](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) för VPNSite för att skapa platsinformationen i Virtuell WAN. Du kan tillhandahålla alla SDWAN/VPN-enheter eller välja enhetsanpassningar efter behov.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Nedladdning och anslutning av enhetskonfiguration

Det här steget innebär att ladda ned Azure-konfiguration och konfigurera anslutning från filialenheten till Azure Virtual WAN. I det här steget skulle en kund som inte använder en provider manuellt hämta Azure-konfigurationen och tillämpa den på sin lokala SDWAN/VPN-enhet. Som leverantör bör du automatisera det här steget. Mer information finns i hämtningen [av REST-API:er.](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) Enhetsstyrenheten kan anropa REST-API :et GetVpnConfiguration för att hämta Azure-konfigurationen.

**Konfigurationsanteckningar**

  * Om Azure VNets är kopplade till den virtuella hubben visas de som ConnectedSubnets.
  * VPN-anslutning använder ruttbaserad konfiguration och stöder både IKEv1- och IKEv2-protokoll.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Konfigurationsfil för enhet

Konfigurationsfilen för enheten innehåller de inställningarna du ska använda när du konfigurerar den lokala VPN-enheten. När du visar den här filen ser du följande information:

* **vpnSiteConfiguration** I det här avsnittet anges enhetsinformation konfigurerad som en plats som ansluter till det virtuella WAN-nätverket. Det omfattar namn och offentlig IP-adress för grenenheten.
* **vpnSiteConnections** – Det här avsnittet innehåller information om följande:

    * **Adressutrymme** för det virtuella navet/vnet.<br>Exempel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adressutrymme** för de virtuella nätverk som är anslutna till navet.<br>Exempel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-adresser** till vpngateway för den virtuella hubben. Eftersom varje anslutning för vpngateway består av två tunnlar i aktiv-aktiv konfiguration ser du båda IP-adresserna listade i den här filen. I det här exemplet ser du "Instance0" och "Instance1" för varje plats.<br>Exempel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Vpngateway anslutningskonfiguration detaljer** såsom BGP, pre-delad nyckel etc. PSK är den fördelade nyckeln som genereras automatiskt åt dig. Du kan alltid redigera anslutningen på översiktssidan för en anpassad PSK.
  
**Konfigurationsfil för exempelenhet**

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
                  "10.3.0.0/16"
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

## <a name="connectivity-details"></a><a name="default"></a>Information om anslutning

Din lokala SDWAN/VPN-enhet eller SD-WAN-konfiguration måste matcha eller innehålla följande algoritmer och parametrar som du anger i Azure IPsec/IKE-principen.

* IKE-krypteringsalgoritm
* IKE-integritetsalgoritm
* DH-grupp
* IPsec-krypteringsalgoritm
* IPsec-integritetsalgoritm
* PFS-grupp

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Standardprinciper för IPsec-anslutning

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Anpassade principer för IPsec-anslutning

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om VirtuellT WAN finns i [Om Azure Virtual WAN](virtual-wan-about.md) och vanliga frågor och svar om Azure Virtual [WAN](virtual-wan-faq.md).

För ytterligare information, skicka ett <azurevirtualwan@microsoft.com>mail till . Ange ditt företagsnamn inom [ ] i ämnesraden.
