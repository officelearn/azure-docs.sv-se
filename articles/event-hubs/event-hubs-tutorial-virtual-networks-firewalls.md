---
title: Självstudier – Aktivera integrering av virtuella nätverk och brandväggar på Event Hubs | Microsoft Docs
description: I den här självstudien får du lära dig hur du integrerar Event Hubs med virtuella nätverk och brandväggar för att möjliggöra säker åtkomst.
services: event-hubs-messaging
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: bd76d8a7f3f41a8aa6b2e614d37f361a98ac4efd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53281320"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Självstudier: Aktivera integrering av virtuella nätverk och brandväggar för Event Hubs-namnområdet

[Med tjänstslutpunkter för Virtual Network (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) får du ett utökat privat adressutrymme för det virtuella nätverket och identiteten för ditt VNet till Azure-tjänsterna, via en direktanslutning. Med slutpunkter kan du skydda dina kritiska Azure-tjänstresurser till endast dina virtuella nätverk. Trafik från ditt VNet till Azure-tjänsten förblir alltid på Microsoft Azure-stamnätverket.

Med brandväggar kan du begränsa åtkomsten till Event Hubs-namnområdet från specifika IP-adresser (eller IP-adressintervall)

Den här självstudiekursen visar hur du integrerar tjänstslutpunkter i virtuella nätverk och konfigurerar brandväggar (IP-filtrering) med ditt befintliga Azure Event Hubs-namnområde med hjälp av portalen.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
> * Integrera tjänstslutpunkter i virtuella nätverk med Event Hubs-namnområdet.
> * Konfigurera brandväggen (IP-filtrering) med Event Hubs-namnområdet.

>[!WARNING]
> Genom att implementera integrering av virtuella nätverk kan du förhindra andra Azure-tjänster från att interagera med Service Bus.
>
> Förstapartsintegreringar stöds inte när virtuella nätverk har aktiverats och kommer att göras tillgängliga snart.
> Vanliga Azure-scenarier som inte fungerar med virtuella nätverk –
> * Azure-diagnostik och loggning
> * Azure Stream Analytics
> * Event Grid-integrering
> * Web Apps och Functions måste finnas i ett virtuellt nätverk.
> * IoT Hub-vägar
> * IoT Device Explorer


> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. De stöds inte på grundläggande nivå.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Vi ska använda ett befintligt Event Hubs-namnområde, så se till att du har ett Event Hubs-namnområde tillgängligt. Om så inte är fallet, så läs [den här självstudien](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Gå först till [Azure-portalen][Azure portal] och logga in med din Azure-prenumeration.

## <a name="select-event-hubs-namespace"></a>Välj Event Hubs-namnområde

För den här självstudiekursen har vi skapat ett Event Hubs-namnområde som vi ska navigera till.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Gå till Brandväggar och Virtuella nätverk

Använd på navigeringsmenyn till vänster i portalen och välj alternativet **Brandväggar och virtuella nätverk**.

  ![Navigera till menyn](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Första gången du besöker den här sidan måste du välja knappen **Alla nätverk**. Detta innebär att Event Hubs-namnområdet tillåter alla inkommande anslutningar.

## <a name="add-virtual-network-service-endpoint"></a>Lägg till slutpunkt för virtuell nätverkstjänst

Om du vill begränsa åtkomsten måste du integrera slutpunkten för virtuell nätverkstjänst för det här Event Hubs-namnområdet.

1. Klicka på knappen **Valda nätverk** överst på sidan och aktivera resten av sidan med menyalternativen.
  ![valda nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. I sidans avsnitt för virtuellt nätverk väljer du alternativet ***+ Lägg till befintligt virtuellt nätverk***. Då öppnas fönstret där du kan välja ett redan skapat virtuellt nätverk.
  ![lägga till ett befintligt virtuellt nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Välj det virtuella nätverket i listan och välj undernätet.
   ![välj undernät](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Du måste aktivera tjänstslutpunkten innan du lägger till det virtuella nätverket i listan. Om tjänstslutpunkten inte är aktiverad, uppmanas du att aktivera det i portalen.
  ![välj undernät och aktivera slutpunkt](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Om du inte kan aktivera tjänstslutpunkten kan du ignorera den saknade slutpunkten för virtuell nätverkstjänst genom att använda ARM-mallen. Den här funktionen är inte tillgänglig i portalen.

5. Efter det att du har aktiverat tjänstslutpunkten för det valda undernätet kan du fortsätta med att lägga till det i listan över tillåtna virtuella nätverk.
  ![lägga till undernät när slutpunkten har aktiverats](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Fortsätta att klicka på knappen **Spara** i menyfliksområdet längst upp och spara den virtuella nätverkskonfigurationen för tjänsten. Vänta några minuter tills bekräftelsen dyker upp bland portalmeddelandena.

## <a name="add-firewall-for-specified-ip"></a>Lägg till en brandvägg för den angivna IP-adressen

Vi kan begränsa åtkomsten till Event Hubs-namnområdet för ett begränsat intervall med IP-adresser eller en specifik IP-adress med hjälp av brandväggsregler.

1. Klicka på knappen **Valda nätverk** överst på sidan och aktivera resten av sidan med menyalternativen.
  ![valda nätverk](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Du kan lägga till en eller flera specifika IP-adresser eller IP-adressintervall i avsnittet **Brandvägg** under rutnätet ***Adressintervall***.
  ![lägg till ip-adresser](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. När du har lagt till flera IP-adresser (eller IP-adressintervall) ser du till att spara konfigurationen på serversidan genom att välja **Spara** i menyfliksområdet högst upp. Vänta några minuter tills bekräftelsen dyker upp bland portalmeddelandena.
  ![lägga till ip-adresser och klicka på spara](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Lägga till din aktuella IP-adress i brandväggsreglerna

1. Du kan också snabbt lägga till din aktuella IP-adress genom att markera kryssrutan ***Lägg till klientens IP-adress (DIN AKTUELLA IP-ADRESS)*** precis ovanför rutnätet ***Adressintervall***.
  ![lägger till den aktuella IP-adressen](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. När du har lagt till din aktuella IP-adress till brandväggsreglerna ser du till att spara konfigurationen på serversidan genom att välja **Spara** i menyfliksområdet högst upp. Vänta några minuter tills bekräftelsen dyker upp bland portalmeddelandena.
  ![lägg till aktuell IP-adress och välj spara](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Sammanfattning

I den här självstudien har du integrerat slutpunkter för virtuellt nätverk och brandväggsregler med ett befintligt Event Hubs-namnområde. Du har lärt dig att:
> [!div class="checklist"]
> * Integrera tjänstslutpunkter i virtuella nätverk med Event Hubs-namnområdet.
> * Konfigurera brandväggen (IP-filtrering) med Event Hubs-namnområdet.


[Azure portal]: https://portal.azure.com/