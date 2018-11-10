---
title: Hantera lagringskonton för Azure Stack | Microsoft Docs
description: Lär dig att hitta, hantera, återställa och frigöra lagringskonton för Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: af7500d6bf12ef34e6919aa255185a87525310b3
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240723"
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Hantera lagringskonton i Azure Stack
Lär dig mer om att hantera lagringskonton i Azure Stack för att hitta, återställa och frigöra lagringskapacitet baserat på affärsbehoven.

## <a name="find"></a>Hitta ett storage-konto
Listan med lagringskonton i regionen kan ses i Azure Stack med:

1. Logga in på den [administrationsportalen](https://adminportal.local.azurestack.external).

2. Välj **alla tjänster** > **regionshantering** under **Administration**.

3. Välj **Storage** från den **Resursprovidrar** lista.
   
   ![Lagringsresursprovider](media/azure-stack-manage-storage-accounts/image1.png)

5. Välj **lagringskonton** i **Storage**.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
   
   Bladet visar listan med lagringskonton i den regionen.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Som standard visas de första 10 kontona. Du kan välja att hämta mer genom att klicka på den **Läs in fler** länken längst ned i listan.

ELLER

Om du är intresserad av ett visst lagringskonto – kan du **filtrera och hämta de relevanta kontona** endast.


**Så här filtrerar för konton:**

1. Välj **Filter** överst i fönstret.
2. I fönstret Filter kan du ange **kontonamn**, ** prenumerations-ID eller **status** att finjustera listan med lagringskonton som ska visas. Använd dem efter behov.
3. Välj **uppdatering**. Listan bör uppdatera därefter.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Att återställa filtret: Välj **Filter**, tar bort valen och uppdatera.

Sökrutan (överst i listvyn för storage-konton) kan du markera den markerade texten i listan över konton. Du kan använda när det fullständiga namnet eller ID: T inte är tillgängliga.

Du kan använda fri text här för att identifiera det konto du är intresserad av.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Titta på kontoinformation
När du har hittat de konton som du är intresserad av, kan du välja kontot att visa vissa detaljer. Ett nytt fönster öppnas med information om kontot som: vilken typ av konto, tidpunkten för skapandet, plats, osv.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Återställa ett Borttaget konto
Du kanske är i en situation där du vill återställa ett Borttaget konto.

Det finns ett enkelt sätt att göra det i Azure Stack:

1. Bläddra till i listan över storage-konton. Se [hitta ett lagringskonto](#find) i den här artikeln för mer information.
2. Leta upp det specifika kontot i listan. Du kan behöva filtrera.
3. Kontrollera den *tillstånd* för kontot. Det ska stå **borttagna**.
4. Välj kontot som öppnar rutan information.
5. Utöver det här fönstret kan du leta upp den **återställa** knappen och markera den.
6. Bekräfta genom att välja **Ja**.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Återställningen är nu i *bearbeta... Vänta* för en indikation på att det lyckades.
   Du kan också välja ”klockikonen” överst i portalen för att visa förloppet uppgifter.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   När kontot återställda har synkroniserats, kan den användas igen.

### <a name="some-gotchas"></a>Vissa saker
* Kontot har tagits bort visar tillstånd som **utanför kvarhållning**.
  
  Utanför kvarhållning innebär att att kontot har överskridit kvarhållningsperioden och kanske inte återställa.
* Kontot har tagits bort visas inte i listan över konton.
  
  Ditt konto kanske inte visas i kontolistan över när kontot har redan skräpinsamlats. I det här fallet kan den inte återställas. Se [frigöra kapacitet](#reclaim) i den här artikeln.

## <a name="set-the-retention-period"></a>Ange kvarhållningsperioden
Period kvarhållningsinställning kan en cloud-operator för att ange en tidsperiod i dagar (mellan 0 och 9 999 dagar) under vilken alla Borttaget konto potentiellt kan återställas. Loggperioden har angetts till 0 dagar. Ange värdet till ”0” betyder att alla borttagna konton är omedelbart utanför kvarhållning och markerats för periodiska skräpinsamling.

**Ändra kvarhållningsperioden:**

1. Logga in på den [administrationsportalen](https://adminportal.local.azurestack.external).
2. Välj **alla tjänster** > **regionshantering** under **Administration**.
3. Välj **Storage** från den **Resursprovidrar** lista.
4. Välj **inställningar** överst för att öppna fönstret inställningen.
5. Välj **Configuration** redigera värdet för kvarhållning perioden.

   Ange antal dagar och spara den.
   
   Det här värdet är omedelbart effektiva och har angetts för en hel region.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Frigöra kapacitet
En av sidoeffekter med en kvarhållningsperiod är att ett Borttaget konto fortsätter att använda kapacitet tills det gäller utanför kvarhållningsperioden. Som en cloud-operatör kan du behöver ett sätt att frigöra utrymme för kontot även om kvarhållningsperioden har löpt.

Du kan frigöra kapacitet med hjälp av portalen eller PowerShell.

**Frigöra kapacitet med hjälp av portalen:**
1. Gå till fönstret storage-konton. Se [hitta ett lagringskonto](#find).
2. Välj **frigöra utrymme** överst i fönstret.
3. Läs meddelandet och välj sedan **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Vänta på lyckade meddelanden finns på klockikonen i portalen.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Uppdatera sidan för Storage-konton. De borttagna kontona är inte längre visas i listan eftersom de har rensats.

Du kan också använda PowerShell för att åsidosätta uttryckligen kvarhållningsperioden och omedelbart frigöra kapacitet.

**Att frigöra kapacitet med hjälp av PowerShell:**   

1. Bekräfta att du har Azure PowerShell installerad och konfigurerad. Om inte, Använd följande instruktioner: 
   * Om du vill installera den senaste versionen av Azure PowerShell och associera den med din Azure-prenumeration, se [hur du installerar och konfigurerar du Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Läs mer om Azure Resource Manager-cmdletar, [med hjälp av Azure PowerShell med Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767)
2. Kör följande cmdlets:

> [!NOTE]  
> Om du kör dessa cmdlet: ar kan du permanent ta bort kontot och dess innehåll. Det går inte att återställa. Använd detta med försiktighet.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Mer information finns i [Azure Stack PowerShell-dokumentationen.](https://docs.microsoft.com/powershell/module/azurerm.azurestackstorage)
 

## <a name="next-steps"></a>Nästa steg

 - Information om hur du hanterar behörigheter finns i [Manage Role-Based Access Control](azure-stack-manage-permissions.md).
 - Information om Hantera lagringskapacitet för Azure Stack finns i [hantera lagringskapacitet för Azure Stack](azure-stack-manage-storage-shares.md).