---
title: Hantera Azure-stacken storage-konton | Microsoft Docs
description: Lär dig att hitta, hantera, återställa och frigöra Stack för Azure storage-konton
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 2ae2b628b2e61893a5289151c3b405e7412e7d13
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="manage-storage-accounts-in-azure-stack"></a>Hantera storage-konton i Azure-stacken
Lär dig hur du hanterar storage-konton i Azure stackutrymme för att hitta, återställa och frigöra lagringskapacitet baserat på affärsbehov.

## <a name="find"></a>Hitta ett lagringskonto
Lista med lagringskonton i region kan visas i Azure-stacken genom att:

1. I en webbläsare, navigerar du till https://adminportal.local.azurestack.external.
2. Logga in på Azure Stack-administrationsportalen som ett moln-operatorn (med de autentiseringsuppgifter du angav under distribution)
3. Leta reda på standardinstrumentpanelen – den **Region management** och väljer den region som du vill utforska, till exempel **(lokala**).
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. Välj **lagring** från den **Resursproviders** lista.
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. Nu, i rutan storage Resource Provider administratör – rulla ned till den **lagringskonton** fliken och markera den.
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   Den resulterande sidan är listan över storage-konton i den regionen.
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

Som standard visas de första 10 kontona. Du kan välja att hämta mer genom att klicka på den **läsa in mer** länken längst ned i listan.

ELLER

Om du är intresserad av ett visst lagringskonto – kan du **filtrera och hämta de relevanta kontona** endast.


**Så här filtrerar för konton:**

1. Välj **Filter** längst upp i fönstret.
2. I rutan Filter kan du ange **kontonamn**, ** prenumerations-ID eller **status** att finjustera listan över storage-konton som ska visas. Använd dem efter behov.
3. Välj **uppdatering**. Därefter uppdatera listan.
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. Så här återställer du filtret: Välj **Filter**, rensa valen och uppdatera.

Sökrutan (överst listvyn storage-konton) kan du markera den markerade texten i listan över konton. Du kan använda när det fullständiga namnet eller ID: T inte är tillgängliga.

Du kan använda fritext här för att hitta det konto som du är intresserad av.

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Titta på kontoinformation
När du har hittat de konton som du är intresserad av kan du välja kontot att visa vissa detaljer. Ett nytt fönster som öppnas med kontoinformation: typ av konto, skapelsetid, plats, osv.

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Återställa ett Borttaget konto
Du kan vara i en situation där du behöver återställa ett Borttaget konto.

Det finns ett enkelt sätt att göra det i Azure Stack:

1. Bläddra till listan storage-konton. Se [hitta ett lagringskonto](#find) i det här avsnittet för mer information.
2. Leta upp det specifika kontot i listan. Du kan behöva filtrera.
3. Kontrollera den *tillstånd* för kontot. Det ska stå **borttagna**.
4. Välj kontot som öppnar informationsfönstret konto.
5. Utöver det här fönstret kan du leta upp den **återställa** knappen och markera den.
6. Välj **Ja** att bekräfta.
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. Återställningen är nu i *bearbeta... Vänta* för en indikation på att det lyckades.
   Du kan också välja ”klockikonen” överst i portalen för att visa förloppet uppgifter.
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   När återställda kontot har synkroniserats kan kan den användas igen.

### <a name="some-gotchas"></a>Vissa saker
* Kontot för borttagna visar tillstånd som **utanför kvarhållning**.
  
  Out-of-kvarhållning innebär att kontot har överskridit kvarhållningsperioden och kan inte gå att återställa.
* Din kontot visas inte i kontolistan över.
  
  Kontot du kanske inte visas i listan över användarkonton när kontot har redan skräpinsamlats. I detta fall kan återställas den inte. Se [frigöra kapacitet](#reclaim) i det här avsnittet.

## <a name="set-the-retention-period"></a>Ställa in kvarhållningsperioden
Kvarhållning period inställningen kan en moln-operatorn för att ange en tidsperiod i dagar (mellan 0 och 9 999 dagar) under vilken alla kontot potentiellt kan återställas. Loggperioden har angetts till 0 dagar. Ange värdet till ”0” betyder att alla borttagna konton är omedelbart utanför kvarhållning och har markerats för periodiska skräpinsamling.

**Ändra kvarhållningsperioden:**

1. I en webbläsare, navigerar du till https://adminportal.local.azurestack.external.
2. Logga in på Azure Stack-administrationsportalen som ett moln-operatorn (med de autentiseringsuppgifter du angav under distribution)
3. Leta reda på standardinstrumentpanelen – den **Region management** och väljer den region som du vill utforska – till exempel **(lokala**).
4. Välj **lagring** från den **Resursproviders** lista.
5. Välj **inställningar** längst upp för att öppna fönstret inställningen.
6. Välj **Configuration** redigera värdet för kvarhållning perioden.

   Ange antalet dagar och spara den.
   
   Det här värdet är omedelbart effektivt och har angetts för hela region.

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Frigöra kapacitet
En av sidoeffekter för med en kvarhållningsperiod är att ett Borttaget konto fortsätter att använda kapacitet tills det kommer utanför kvarhållningsperioden. Som en moln-operator får du behöver ett sätt att frigöra utrymme för kontot även om loggperioden inte har ännu gått.

Du kan frigöra kapacitet med hjälp av portalen eller PowerShell.

**Att frigöra kapacitet med hjälp av portalen:**
1. Gå till fönstret storage-konton. Se [hitta ett lagringskonto](#find).
2. Välj **frigöra utrymme** längst upp i fönstret.
3. Läsa meddelandet och välj sedan **OK**.

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. Vänta tills den lyckas meddelandet finns på klockikonen på portalen.

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. Uppdatera sidan Storage-konton. Borttagna konton visas inte längre i listan, eftersom de har rensats.

Du kan också använda PowerShell för att åsidosätta explicit kvarhållningsperioden och omedelbart frigöra kapacitet.

**Att frigöra kapacitet med hjälp av PowerShell:**   

1. Bekräfta att du har Azure PowerShell installeras och konfigureras. Om inte, Använd följande instruktioner: 
   * Om du vill installera den senaste versionen av Azure PowerShell och koppla den till din Azure-prenumeration, se [hur du installerar och konfigurerar du Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Mer information om Azure Resource Manager cmdlets finns [med hjälp av Azure PowerShell med Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. Kör följande cmdlets:

> [!NOTE]
> Om du kör dessa cmdletar kan du permanent ta bort kontot och dess innehåll. Den kan inte återställas. Använd det här med försiktighet.

```PowerShell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
````

Mer information finns i [Azure Stack PowerShell-dokumentationen.](https://msdn.microsoft.com/library/mt637964.aspx)
 

## <a name="next-steps"></a>Nästa steg

 - Information om hur du hanterar behörigheter finns i [Manage Role-Based Access Control](azure-stack-manage-permissions.md).
 - Mer information om Hantera lagringskapacitet för Azure-stacken finns [hantera lagringskapacitet för Azure-stacken](azure-stack-manage-storage-shares.md).