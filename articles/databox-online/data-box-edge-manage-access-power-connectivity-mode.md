---
title: Azure Data Box Edge Enhetsåtkomst, kraft och anslutningsläget | Microsoft Docs
description: Beskriver hur du hanterar åtkomst, kraft och anslutningsläget för Azure Data Box Edge-enhet som hjälper dig att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 8937f4c47f0fa84d4ec371e951cff8a2fdaa8481
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476898"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Hantera åtkomst, power och anslutningsläget för Azure Data Box-Edge

Den här artikeln beskriver hur du hanterar åtkomst, kraft och anslutningen läge för din Azure Data Box Edge. Dessa åtgärder utförs via det lokala webbgränssnittet eller Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera Enhetsåtkomst
> * Hantera anslutningsläget
> * Hantera power


## <a name="manage-device-access"></a>Hantera Enhetsåtkomst

Åtkomst till din Data Box Edge-enhet är kontrolleras med hjälp av enhetens lösenord. Du kan ändra lösenordet via det lokala webbgränssnittet. Du kan också återställa lösenordet för enheten i Azure-portalen.

### <a name="change-device-password"></a>Ändra enhetens lösenord

Följ dessa steg i det lokala Användargränssnittet för att ändra lösenordet för enheten.

1. I det lokala webbgränssnittet går du till **Underhåll > lösenordsändring**.
2. Ange det aktuella lösenordet och sedan det nya lösenordet. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Bekräfta det nya lösenordet.

    ![Ändra lösenord](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Välj **ändra lösenord**.
 
### <a name="reset-device-password"></a>Återställa enhetens lösenord

Återställ arbetsflöde kräver inte användare att komma ihåg det gamla lösenordet och är användbart när lösenordet går förlorat. Det här arbetsflödet utförs i Azure-portalen.

1. I Azure-portalen går du till **översikt > Återställ administratörslösenord**.

    ![Återställ lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Ange det nya lösenordet och bekräftar det sedan. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Välj **återställa**.

    ![Återställ lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Hantera åtkomst till resurser

För att skapa din Data Box Edge/Data Box-Gateway, IoT Hub och Azure Storage-resursen, behöver du behörigheter som deltagare eller högre på en resursgruppsnivå. Du måste också motsvarande resursprovidrar som ska registreras. För att utföra åtgärder som rör aktiveringsnyckeln och autentiseringsuppgifter krävs också behörighet till Azure Active Directory Graph API. Dessa beskrivs i följande avsnitt.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Hantera Microsoft Azure Active Directory Graph API-behörigheter

När du genererar aktiveringsnyckeln för Data Box Edge-enhet eller utföra åtgärder som kräver autentiseringsuppgifter behöver behörigheter till Azure Active Directory Graph API. De åtgärder som måste ha autentiseringsuppgifter som kan vara följande:

-  Skapa en resurs med ett associerat lagringskonto.
-  Skapa en användare som har åtkomst till resurser på enheten.

Du bör ha en `User` åtkomst till i Active Directory-klient som du behöver för att kunna `Read all directory objects`. Du kan inte vara en gästanvändare som de inte har behörighet att `Read all directory objects`. Om du är en gäst och åtgärder som till exempel generering av en aktiveringsnyckel för, skapa en resurs på din Data Box Edge-enhet sedan, misslyckas skapandet av en användare alla.

Mer information om hur du ger åtkomst till användare till Azure Active Directory Graph API finns i [standard åtkomst för administratörer, användare och gästanvändare](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Registrera resursprovidrar

För att etablera en resurs i Azure (i Azure Resource Manager-modellen), behöver du en provider för nätverksresurser som har stöd för skapandet av den här resursen. Du bör till exempel ha en ”Microsoft.Compute-resursprovidern som är tillgängliga i prenumerationen för att etablera en virtuell dator.
 
Resursproviders är registrerad på nivån för prenumerationen. Alla nya Azure-prenumerationer är redan registrerad med en lista över vanliga resursproviders som standard. Resursprovidern för 'Microsoft.DataBoxEdge' ingår inte i den här listan.

Du behöver inte bevilja behörigheter för åtkomst till prenumerationsnivå för användare för att kunna skapa resurser som ”Microsoft.DataBoxEdge” i sina resursgrupper som de har ägarrättigheter på, så länge resursprovidrar för dessa resurser används redan registrerad.

Kontrollera att resursprovidern har registrerats i prenumerationen innan du försöker skapa en resurs. Om resursprovidern inte är registrerat, behöver du se till att den användare som skapar den nya resursen har tillräcklig behörighet för att registrera resursprovidern som krävs på prenumerationsnivån. Om du inte gjort detta samt, ser du följande fel:

*Prenumerationen <Subscription name> har inte behörighet att registrera resursprovidersarna: Microsoft.DataBoxEdge.*


Om du vill hämta en lista över registrerade resursprovidrar i den aktuella prenumerationen, kör du följande kommando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

För Data Box Edge-enhet, `Microsoft.DataBoxEdge` ska registreras. Att registrera `Microsoft.DataBoxEdge`, prenumerationsadministratör bör köra följande kommando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Läs mer om hur du registrerar en resursprovider [åtgärda fel för registreringen av resursprovidern](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Hantera anslutningsläget

Förutom de fullständigt anslutna standardläget, kan enheten också köra i delvis ansluten eller helt frånkopplad läge. Var och en av dessa lägen beskrivs nedan:

- **Anslutas fullständigt** – detta är normalt standardläget där enheten fungerar. Både molnöverföring och hämtning av data är aktiverat i det här läget. Du kan använda Azure portal eller det lokala webbgränssnittet för att hantera enheten.

- **Delvis frånkopplad** – i det här läget enheten kan inte överföra eller hämta alla dela data men kan hanteras via Azure portal.

    Det här läget används vanligtvis när du är i ett bevakat satelliten nätverk och målet är att minimera bandbreddsanvändning i nätverket. Minimal nätverksförbrukning kan fortfarande ha åtgärder för enhetsövervakning.

- **Frånkopplad** – i det här läget enheten är helt frånkopplad från molnet och både i molnet överföringar och hämtningar har inaktiverats. Enheten kan bara hanteras via det lokala webbgränssnittet.

    Det här läget används vanligtvis när du vill koppla från enheten.

Följ dessa steg om du vill ändra Enhetsläge:

1. I det lokala webbgränssnittet på din enhet, går du till **Configuration > Molninställningar**.
2. Välj det läge som du vill använda enheten i den nedrullningsbara listan. Du kan välja från **anslutas fullständigt**, **delvis ansluten**, och **helt frånkopplad**. Om du vill köra enheten i delvis frånkopplat läge, aktivera **hantering av Azure portal**.

    ![Läge för dataanslutning](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Hantera power

Du kan stänga av eller starta om den fysiska enheten med hjälp av det lokala webbgränssnittet. Vi rekommenderar att innan du startar om ta resurser offline på data-server och sedan på enheten. Den här åtgärden minimerar risk att data skadas.

1. I det lokala webbgränssnittet går du till **Underhåll > energiinställningar**.
2. Välj **avstängning** eller **starta om** beroende på vad du vill göra.

    ![Energiinställningar](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta väljer **Ja** att fortsätta.

> [!NOTE]
> Om du stänger av den fysiska enheten kommer du behöva på strömknappen på enheten för att aktivera den.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hantera filresurser](data-box-edge-manage-shares.md).