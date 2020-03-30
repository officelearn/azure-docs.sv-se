---
title: Aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services (UI) | Microsoft-dokument
description: Lär dig hur du skapar en virtuell mall med flera virtuella datorer inuti.  Med andra ord aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503080"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services manuellt

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i ett labbs virtuella malldator. Publicera mallen kommer att ge varje användare i labbet med en virtuell dator som inrättats med flera virtuella datorer i den.  Mer information om kapslad virtualisering och Azure Lab Services finns i [Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Den här artikeln beskriver hur du konfigurerar kapslad virtualisering på en malldator i Azure Lab Services med Windows-roller och verktyg direkt.  Det finns några saker som behövs för att en klass ska kunna använda kapslad virtualisering.  Stegen nedan beskriver hur du manuellt ställer in en Lab Services-maskinmall med Hyper-V.  Stegen är avsedda för Windows Server 2016 eller Windows Server 2019.  

>[!IMPORTANT]
>Välj **Stor (kapslad virtualisering)** eller **Medel (kapslad virtualisering)** för den virtuella datorns storlek när du skapar labbet.  Kapslad virtualisering fungerar inte annars.  

## <a name="enable-hyper-v-role"></a>Aktivera hyper-V-roll

I följande steg beskrivs åtgärder som behövs för att aktivera Hyper-V på Windows Server med serverhanteraren.  När installationen har slutförts kan Hyper-V-chefen vara tillgänglig för att lägga till, ändra och ta bort virtuella klientdatorer.

1. Klicka på Lägg till roller **och funktioner**på instrumentpanelssidan i **Serverhanteraren**.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj installationstyp** behåller du standardvalet rollbaserad eller funktionsbaserad installation och klickar sedan på **Nästa**.
4. På sidan **Välj målserver** väljer du Välj en server i serverpoolen.   Den aktuella servern har redan valts.  Klicka på Nästa.
5. Välj **Hyper-V**på sidan **Välj serverroller** .  
6. Popup-programmet **Lägg till roller och funktioner** visas.  Välj **Inkludera hanteringsverktyg (om tillämpligt)**.  Klicka på knappen **Lägg till funktioner.**
7. På sidan **Välj serverroller**, klickar du på **Nästa**.
8. Klicka på **Nästa**på **sidan Välj funktioner**.
9. På sidan **Hyper-V** klickar du på **Nästa**.
10. På sidan **Skapa virtuella växlar** godkänner du standardinställningarna och klickar på **Nästa**.
11. På sidan **Migrering för virtuella** datorer godkänner du standardinställningarna och klickar på **Nästa**.
12. På sidan **Standardbutiker** godkänner du standardinställningarna och klickar på **Nästa**.
13. På sidan **Bekräfta installationsval** väljer du **Starta om målservern automatiskt om det behövs**.
14. När **popup-programmet Lägg till roller och funktioner** visas klickar du på **Ja**.
15. Klicka på **Installera**.
16. Vänta **tills sidan Installationsförlopp** anger att Hyper-V-rollen är klar.  Maskinen kan startas om mitt i installationen.
17. Klicka på **Stäng**.

## <a name="enable-dhcp-role"></a>Aktivera DHCP-roll

Alla virtuella Hyper-V-klientdatorer som skapas behöver en IP-adress i NAT-nätverket.  Vi skapar NAT-nätverket senare.  Ett sätt att tilldela IP-adresser är att ställa in värden, i det här fallet labbmallen för virtuella datorer, som en DHCP-server.  Nedan följer de steg som krävs för att aktivera DHCP-rollen.

1. Klicka på Lägg till roller **och funktioner**på **instrumentpanelssidan** i **Serverhanteraren**.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj installationstyp** väljer du **Rollbaserad eller funktionsbaserad installation** och klickar sedan på **Nästa**.
4. På sidan **Välj målserver** väljer du den aktuella servern från serverpoolen och klickar sedan på **Nästa**.
5. Välj **DHCP Server**på sidan Välj **serverroller** .  
6. Popup-programmet **Lägg till roller och funktioner** visas.  Välj **Inkludera hanteringsverktyg (om tillämpligt)**.  Klicka på **Lägg till funktioner**.

    >[!NOTE]
    >Du kan se ett valideringsfel som anger att inga statiska IP-adresser hittades.  Den här varningen kan ignoreras för vårt scenario.

7. På sidan **Välj serverroller**, klickar du på **Nästa**.
8. Klicka på **Nästa**på sidan **Välj funktioner** .
9. Klicka på **Nästa**på sidan **DHCP-server** .
10. Klicka på **Installera** på sidan **Bekräfta installationsval**.
11. Vänta tills **sidan Installationsstatus** anger att DHCP-rollen är klar.
12. Klicka på Stäng.

## <a name="enable-routing-and-remote-access-role"></a>Aktivera rollen Routning och fjärråtkomst

1. Klicka på Lägg till roller **och funktioner**på **instrumentpanelssidan** i **Serverhanteraren**.
2. Klicka på **Nästa** på sidan **Innan du börjar**.
3. På sidan **Välj installationstyp** väljer du **Rollbaserad eller funktionsbaserad installation** och klickar sedan på **Nästa**.
4. På sidan **Välj målserver** väljer du den aktuella servern från serverpoolen och klickar sedan på **Nästa**.
5. Välj **Fjärråtkomst**på sidan **Välj serverroller** . Klicka på **OK**.
6. Klicka på **Nästa**på sidan **Välj funktioner** .
7. Klicka på **Nästa**på sidan **Fjärråtkomst** .
8. På sidan **Rolltjänster** väljer du **Routning**.
9. Popup-programmet **Lägg till roller och funktioner** visas.  Välj **Inkludera hanteringsverktyg (om tillämpligt)**.  Klicka på **Lägg till funktioner**.
10. Klicka på **Nästa**.
11. Klicka på **Nästa** på sidan **Rollen Webbserver (IIS)**.
12. Klicka på **Nästa** på sidan **Välj rolltjänster**.
13. Klicka på **Installera** på sidan **Bekräfta installationsval**.
14. Vänta tills sidan **Installationsstatus** anger att fjärråtkomstrollen är klar.  
15. Klicka på **Stäng**.

## <a name="create-virtual-nat-network"></a>Skapa virtuellt NAT-nätverk

Nu när alla nödvändiga roller har installerats är det dags att skapa NAT-nätverket.  Skapandeprocessen innebär att skapa en switch och NAT-nätverket, själv.  Ett NAT-nätverk (network address translation) tilldelar en offentlig IP-adress till en grupp virtuella datorer i ett privat nätverk för att möjliggöra anslutning till Internet.  I vårt fall kommer gruppen av privata virtuella datorer att vara de kapslade virtuella datorerna.  NAT-nätverket gör det möjligt för de kapslade virtuella datorerna att kommunicera med varandra. En växel är en nätverksenhet som hanterar mottagning och routning av trafik i ett nätverk.

### <a name="create-a-new-virtual-switch"></a>Skapa en ny virtuell växel

1. Öppna **Hyper-V Manager** från Administrationsverktyg för Windows.
2. Välj den aktuella servern i menyn för vänster navigering.
3. Klicka på **Virtual Switch Manager...** från **menyn Åtgärder** till höger i **Hyper-V-hanteraren**.
4. På popup-programmet **Virtual Switch Manager** väljer du **Internt** för den typ av växel som ska skapas.  Klicka på **Skapa virtuell växel**.
5. För den nyskapade virtuella växeln ställer du in namnet på något minnesvärt.  I det här exemplet använder vi "LabServicesSwitch".  Klicka på **OK**.
6. Ett nytt nätverkskort skapas.  Namnet kommer att likna 'vEthernet (LabServicesSwitch)'.  Om du vill kontrollera att **Kontrollpanelen**öppnas klickar du på **Nätverk och Internet**och klickar på Visa **nätverksstatus och uppgifter**.  Klicka på **Ändra kortinställningar**till vänster .

### <a name="create-a-nat-network"></a>Skapa ett NAT-nätverk

1. Öppna verktyget **Routning och fjärråtkomst** från Administrationsverktygen i Windows.
2. Välj den lokala servern på den vänstra navigeringssidan.
3. Välj **Åtgärd** -> **konfigurera och aktivera routning och fjärråtkomst**.
4. När **guiden Konfigurera routning och fjärråtkomstserver** visas klickar du på **Nästa**.
5. Välj **NAT-konfiguration (Network address translation)** på sidan **Konfiguration.**  Klicka på **Nästa**.

    >[!WARNING]
    >Välj inte alternativet "Virtuellt privat nätverk (VPN) och NAT".

6. På **sidan NAT Internet-anslutning** väljer du "Ethernet".  Välj inte anslutningen "vEthernet (LabServicesSwitch)" som vi skapade i Hyper-V Manager. Klicka på **Nästa**.
7. Klicka på **Slutför** på sista sidan i guiden.
8. När dialogrutan **Starta tjänsten** visas klickar du på **Starta tjänst**.
9. Vänta tills tjänsten har startats.

## <a name="update-network-adapter-settings"></a>Uppdatera inställningar för nätverkskort

Nätverkskortet associeras med den IP som används för standardgateway-IP för NAT-nätverket som skapats tidigare.  I det här exemplet skapar vi en IP-adress på 192.168.0.1 med en nätmask på 255.255.255.0.  Vi kommer att använda den virtuella växeln som skapats tidigare.

1. Öppna **Kontrollpanelen,** klicka på **Nätverk och Internet**, klicka på Visa **nätverksstatus och uppgifter**.
2. Klicka på **Ändra kortinställningar**till vänster .  
3. I fönstret **Nätverksanslutningar** dubbelklickar du på "vEthernet (LabServicesSwitch)" för att visa dialogrutan statusinformation för **vEthernet (LabServicesSwitch).**
4. Klicka på knappen **Egenskaper.**
5. Välj **TCP/IPv4-objekt (Internet Protocol Version 4)** och klicka på knappen **Egenskaper.**
6. I dialogrutan **Egenskaper för Internet Protocol Version 4 (TCP/IPv4)** väljer du Använd följande **IP-adress**.  För IP-adressen anger du 192.168.0.1. För nätmasken anger du 255.255.255.0.  Lämna standardgatewayen tom.  Lämna DNS-servrarna tomma också.

    >[!NOTE]
    > Vårt sortiment för vårt NAT-nätverk kommer att vara, i CIDR-notation, 192.168.0.0/24.  Detta skapar en rad användbara IP-adresser från 192.168.0.1 till 192.168.0.254.  Av konvention har gateways den första IP-adressen i ett undernätsområde.

7. Klicka på OK.

## <a name="create-dhcp-scope"></a>Skapa DHCP-scope

Följande steg är instruktioner för att lägga till DHCP-scope.  I den här artikeln är vårt NAT-nätverk 192.168.0.0/24 i CIDR-notation.  Detta skapar en rad användbara IP-adresser från 192.168.0.1 till 192.168.0.254.  Scopet som skapas måste finnas i det intervall av användbara adresser exklusive IP-adressen som redan skapats tidigare.

1. Öppna **administrationsverktyg** och öppna **DHCP-administrationsverktyget.**
2. Expandera noden för den aktuella servern i **DHCP-verktyget** och välj **IPv4**.
3. Välj **Nytt scope** på Åtgärdsmenyn...
4. När **guiden Nytt scope** visas klickar du på **Nästa** på **välkomstsidan.**
5. På sidan **Scope-namn** anger du "LabServicesDhcpScope" eller något annat minnesvärt för namnet.  Klicka på **Nästa**.
6. Ange följande värden på sidan **IP-adressintervall.**

    - 192.168.0.100 för Start-IP-adressen
    - 192.168.0.200 för slut-IP-adressen
    - 24 för längden
    - 255.255.255.0 för nätmasken

7. Klicka på **Nästa**.
8. Klicka på **Nästa**på sidan **Lägg till undantag och fördröjning** .
9. Klicka på **Nästa**på sidan **Lånetid** .
10. På sidan **Konfigurera DHCP-alternativ** väljer **du Ja, jag vill konfigurera dessa alternativ nu**. Klicka på **Nästa**.
11. På **routern (standardgateway)**
12. Lägg till 192.168.0.1, om inte gjort redan. Klicka på **Nästa**.
13. Lägg till 168.63.129.16 som EN DNS-server-IP-adress på sidan Domännamn **och DNS-servrar,** om den inte redan är klar.  168.63.129.16 är IP-adressen för en statisk DNS-server i Azure. Klicka på **Nästa**.
14. Klicka på **Nästa**på sidan **WINS-servrar** .
15. En av sidan **Aktivera scope,** välj **Ja, jag vill aktivera det här scopet nu**.  Klicka på **Nästa**.
16. Klicka på **Slutför**på sidan **Slutför guiden Nytt scope** .

## <a name="conclusion"></a>Slutsats

Nu är malldatorn redo att skapa virtuella Hyper-V-datorer.   Se [Skapa en virtuell dator i Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) för instruktioner om hur du skapar virtuella Hyper-V-datorer.  Se även [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) för att kolla in tillgängliga operativsystem och programvara.

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)