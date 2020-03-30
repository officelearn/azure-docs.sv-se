---
title: Åtkomstläge för Azure Data Box Edge-enhet | Microsoft-dokument
description: Beskriver hur du hanterar åtkomst-, energi- och anslutningsläge för Azure Data Box Edge-enheten som hjälper till att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: be4b780161003470622cb367d78138cfeffe341b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454340"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Hantera åtkomst-, energi- och anslutningsläge för din Azure Data Box Edge

I den här artikeln beskrivs hur du hanterar åtkomst-, energi- och anslutningsläget för din Azure Data Box Edge. Dessa åtgärder utförs via det lokala webbgränssnittet eller Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera enhetsåtkomst
> * Hantera anslutningsläge
> * Hantera ström


## <a name="manage-device-access"></a>Hantera enhetsåtkomst

Åtkomsten till din Data Box Edge-enhet styrs med hjälp av ett enhetslösenord. Du kan ändra lösenordet via det lokala webbgränssnittet. Du kan också återställa enhetslösenordet i Azure-portalen.

### <a name="change-device-password"></a>Ändra enhetens lösenord

Följ dessa steg i det lokala användargränssnittet för att ändra enhetens lösenord.

1. Gå till **Ändring av underhåll > lösenord**i det lokala webbgränssnittet .
2. Ange det aktuella lösenordet och sedan det nya lösenordet. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste ha tre av följande tecken: versaler, gemener, numeriska tecken och specialtecken. Bekräfta det nya lösenordet.

    ![Ändra lösenord](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Välj **Ändra lösenord**.
 
### <a name="reset-device-password"></a>Återställa enhetslösenord

Återställningsarbetsflödet kräver inte att användaren återkallar det gamla lösenordet och är användbart när lösenordet går förlorat. Det här arbetsflödet utförs i Azure-portalen.

1. Gå till **Översikt > Återställ administratörslösenordet i**Azure-portalen .

    ![Återställa lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Ange det nya lösenordet och bekräfta det. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste ha tre av följande tecken: versaler, gemener, numeriska tecken och specialtecken. Välj **Återställ**.

    ![Återställa lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Hantera företagsresurser

Om du vill skapa din Data Box Edge/Data Box Gateway, IoT Hub och Azure Storage-resurs behöver du behörigheter som deltagare eller högre på resursgruppsnivå. Du behöver också motsvarande resursleverantörer som ska registreras. För alla åtgärder som involverar aktiveringsnyckel och autentiseringsuppgifter krävs också behörigheter till Microsoft Graph API. Dessa beskrivs i följande avsnitt.

### <a name="manage-microsoft-graph-api-permissions"></a>Hantera behörigheter för Microsoft Graph-API

När du genererar aktiveringsnyckeln för Data Box Edge-enheten eller utför åtgärder som kräver autentiseringsuppgifter behöver du behörigheter till Microsoft Graph API. De åtgärder som behöver autentiseringsuppgifter kan vara:

-  Skapa en resurs med ett associerat lagringskonto.
-  Skapa en användare som kan komma åt resurserna på enheten.

Du bör `User` ha åtkomst till Active Directory-klienten `Read all directory objects`som du behöver för att kunna . Du kan inte vara gästanvändare eftersom de inte `Read all directory objects`har behörighet till . Om du är gäst, då åtgärder som generering av en aktiveringsnyckel, skapandet av en resurs på din Data Box Edge-enhet, skapandet av en användare, konfiguration av Edge beräkningsroll, återställa enhetens lösenord kommer alla att misslyckas.

Mer information om hur du ger användare åtkomst till Microsoft Graph API finns i [Microsoft Graph-behörighetsreferens .](https://docs.microsoft.com/graph/permissions-reference)

### <a name="register-resource-providers"></a>Registrera resursleverantörer

Om du vill etablera en resurs i Azure (i Azure Resource Manager-modellen) behöver du en resursleverantör som stöder skapandet av resursen. Om du till exempel vill etablera en virtuell dator bör du ha en Microsoft.Compute-resursleverantör tillgänglig i prenumerationen.
 
Resursprovidrar registreras på prenumerationsnivån. Alla nya Azure-prenumerationer är som standard förregistrerade med en lista över vanliga resursprovidrar. Resursleverantören för Microsoft.DataBoxEdge finns inte med i den här listan.

Du behöver inte bevilja åtkomstbehörigheter till prenumerationsnivån för att användare ska kunna skapa resurser som "Microsoft.DataBoxEdge" inom sina resursgrupper som de har ägarrättigheter på, så länge resursleverantörerna för dessa resurser redan finns Registrerade.

Innan du försöker skapa en resurs kontrollerar du att resursleverantören är registrerad i prenumerationen. Om resursleverantören inte är registrerad måste du se till att användaren som skapar den nya resursen har tillräckligt med behörighet för att registrera den resursprovider som krävs på prenumerationsnivå. Om du inte har gjort detta också visas följande fel:

*\<Prenumerationsnamnet> har inte behörighet att registrera resursleverantörerna:Microsoft.DataBoxEdge.*


Om du vill hämta en lista över registrerade resursleverantörer i den aktuella prenumerationen kör du följande kommando:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

För Data Box `Microsoft.DataBoxEdge` Edge-enhet, bör registreras. Om `Microsoft.DataBoxEdge`du vill registrera dig bör prenumerationsadministratören köra följande kommando:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Mer information om hur du registrerar en resursprovider finns i [Lösa fel för registrering av resursprovider](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Hantera anslutningsläge

Förutom standardläget som är helt anslutet kan enheten även köras i delvis anslutet eller helt frånkopplat läge. Vart och ett av dessa lägen beskrivs enligt nedan:

- **Helt ansluten** - Detta är det normala standardläget där enheten fungerar. Både molnet ladda upp och ladda ner data är aktiverat i detta läge. Du kan använda Azure-portalen eller det lokala webbgränssnittet för att hantera enheten.

- **Delvis frånkopplad** – I det här läget kan enheten inte ladda upp eller hämta några delningsdata, men kan hanteras via Azure-portalen.

    Det här läget används vanligtvis när det finns i ett satellitnätverk med datapriser och målet är att minimera förbrukningen av nätverksbandbredd. Minimal nätverksförbrukning kan fortfarande uppstå för enhetsövervakningsåtgärder.

- **Frånkopplad** – I det här läget är enheten helt frånkopplad från molnet och både molnet laddas upp och nedladdningar är inaktiverade. Enheten kan endast hanteras via det lokala webbgränssnittet.

    Det här läget används vanligtvis när du vill koppla från enheten.

Så här ändrar du enhetsläge:

1. I enhetens lokala webbgränssnitt går du till **Konfiguration > Molninställningar**.
2. Välj det läge som du vill använda enheten i i listrutan. Du kan välja mellan **Helt ansluten,** **delvis ansluten**och **helt frånkopplad**. Om du vill köra enheten i delvis frånkopplat läge aktiverar du **Azure Portal Management**.

    ![Anslutningsläge](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Hantera ström

Du kan stänga av eller starta om den fysiska enheten med hjälp av det lokala webbgränssnittet. Vi rekommenderar att du tar resurserna offline på dataservern och sedan enheten innan du startar om. Den här åtgärden minimerar risken för dataskador.

1. Gå till **Underhåll > Energiinställningar i**det lokala webbgränssnittet .
2. Välj **Avstängning** eller **Starta om** beroende på vad du tänker göra.

    ![Energiinställningar](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta väljer du **Ja** för att fortsätta.

> [!NOTE]
> Om du stänger av den fysiska enheten måste du trycka på strömbrytaren på enheten för att slå på den.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [hanterar resurser](data-box-edge-manage-shares.md).
