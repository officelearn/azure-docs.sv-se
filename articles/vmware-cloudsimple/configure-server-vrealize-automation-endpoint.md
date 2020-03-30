---
title: Azure VMware Solution by CloudSimple – Konfigurera vCenter på Private Cloud för vRealize Automation
description: Beskriver hur du konfigurerar en VMware vCenter-server på ditt CloudSimple Private Cloud som en slutpunkt för VMware vRealize Automation
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df73acfc469a8b7b5329b61095aefdbd73baafd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024848"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Konfigurera vCenter på ditt privata moln för VMware vRealize Automation

Du kan konfigurera en VMware vCenter-server på ditt CloudSimple Private Cloud som en slutpunkt för VMware vRealize Automation.

## <a name="before-you-begin"></a>Innan du börjar

Slutför dessa uppgifter innan du konfigurerar vCenter-servern:

* Konfigurera en [VPN-anslutning](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) från plats till plats mellan din lokala miljö och ditt privata moln.
* [Konfigurera DNS-vidarebefordran av lokala DNS-begäranden](on-premises-dns-setup.md) till DNS-servrarna för ditt privata moln.
* Skicka en [supportbegäran för](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) att skapa en vRealize Automation IaaS-administratörsanvändare med den uppsättning behörigheter som visas i följande tabell.

| Attributvärde | Behörighet |
------------ | ------------- |  
| Datalager |  Allokera utrymme <br> Bläddra i Datastore |
| Datalagerkluster | Konfigurera ett datalagerkluster |
| Mapp | Skapa mapp <br>Ta bort mapp |
| Global |  Hantera anpassade attribut<br>Ange anpassat attribut |
| Nätverk | Tilldela nätverk |
| Behörigheter | Ändra behörigheter |
| Resurs | Tilldela virtuell dator till resurspool<br>Migrera avstängd virtuell dator<br>Migrera påslagen virtuell dator |
| Inventering av virtuella datorer |  Skapa från befintliga<br>Skapa Ny<br>Flytta<br>Ta bort | 
| Interaktion med virtuella datorer |  Konfigurera CD-media<br>Interaktion mellan konsoler<br>Enhetsanslutning<br>Stäng av strömmen<br>Slå på<br>Återställ<br>Suspend<br>Installation av verktyg | 
| Konfiguration av virtuella datorer |  Lägga till befintlig disk<br>Lägg till ny disk<br>Lägga till eller ta bort<br>Ta bort disk<br>Avancerat<br>Ändra CPU-antal<br>Ändra resurs<br>Utöka virtuell disk<br>Spårning av diskändringar<br>Minne<br>Ändra enhetsinställningar<br>Byt namn<br>Ange anteckning (version 5.0 och senare)<br>Inställningar<br>Placering av växlingsfil |
| Etablering |  Anpassa<br>Mall för klon<br>Klona virtuell dator<br>Distribuera mallen<br>Läs anpassningsspecifikationer |
| Tillstånd för virtuell dator | Skapa ögonblicksbild<br>Ta bort ögonblicksbild<br>Återgå till ögonblicksbild |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installera vRealize Automation i din lokala miljö

1. Logga in på serverinstallationen vRealize Automation IaaS som IaaS-administratör som CloudSimple-supporten har skapat åt dig.
2. Distribuera en vSphere Agent för slutpunkten vRealize Automation.
    1. Gå till https://*vra-url*:5480/installer, där *vra-url* är webbadressen som du använder för att komma åt användargränssnittet för vRealize Automation administration.
    2. Klicka på **IaaS Installer** för att hämta installationsprogrammet.<br>
    Namngivningskonventionen för installationsfilen är setup_*vra-url*@5480.exe.
    3. Kör installationsprogrammet. Klicka på **Nästa** på välkomstskärmen.
    4. Acceptera licensavtalet och klicka på **Nästa**.
    5. Ange inloggningsinformation, klicka på **Acceptera certifikat**och klicka sedan på **Nästa**.
    ![vRA-referenser](media/configure-vra-endpoint-login.png)
    6. Välj **Anpassade installations-** och **proxyagenter** och klicka på **Nästa**.
    ![vRA-installationstyp](media/configure-vra-endpoint-install-type.png)
    7. Ange inloggningsinformation för IaaS-servern och klicka på **Nästa**. Om du använder Active Directory anger du användarnamnet i **domän\användarformat.** Annars använder **user@domain** du format.
    ![vRA inloggningsinformation](media/configure-vra-endpoint-account.png)
    8. För proxyinställningarna anger du **vSphere** för **agenttyp**. Ange ett namn på agenten.
    9. Ange IaaS-servern FQDN i **fälten Manager Service Host** och **Model Manager Web Service Host.** Klicka på **Testa** om du vill testa anslutningen för vart och ett av FQDN-värdena. Om testet misslyckas ändrar du DNS-inställningarna så att IaaS-serverns värdnamn matchas.
    10. Ange ett namn för vCenter-serverslutpunkten för det privata molnet. Registrera namnet för användning senare i konfigurationsprocessen.

        ![vRA installera proxy](media/configure-vra-endpoint-proxy.png)

    11. Klicka på **Nästa**.
    12. Klicka på **Installera**.

## <a name="configure-the-vsphere-agent"></a>Konfigurera vSphere-agenten

1. Gå till https://*vra-url*/vcac och logga in som **ConfigurationAdmin**.
2. Välj > **Slutpunkter** >  **för****infrastrukturslutpunkter**.
3. Välj **Ny** > **virtuell** > **vSphere**.
4. Ange det vSphere-slutpunktsnamn som du angav i föregående procedur.
5. För **Adress**anger du URL:en för privat moln vCenter Server i formatet https://*vcenter-fqdn*/sdk, där *vcenter-fqdn* är namnet på vCenter-servern.
6. Ange autentiseringsuppgifterna för den vRealize Automation IaaS-administratörsanvändare som CloudSimple-supporten har skapat åt dig.
7. Klicka på **Testa anslutning** om du vill verifiera användarautentiseringsuppgifterna. Om testet misslyckas kontrollerar du URL:en, kontoinformation och [slutpunktsnamn](#verify-the-endpoint-name) och testar igen.
8. Efter ett lyckat test klickar du på **OK** för att skapa vSphere-slutpunkten.
    ![vRA-slutpunkt config åtkomst](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Verifiera slutpunktsnamnet

Så här identifierar du rätt vCenter-serverslutpunktsnamn:

1. Öppna en kommandotolk på IaaS-apparaten.
2. Ändra katalogen till C:\Program-filer (x86)\VMware\vCAC\Agents\agent-name, där *agentnamnet* är det namn som du tilldelat till vCenter-serverslutpunkten.
3. Kör följande kommando.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Utdata liknar följande. Värdet för `managementEndpointName` fältet är slutpunktsnamnet.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
