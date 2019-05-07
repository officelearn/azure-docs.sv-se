---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cb7cf932433927d4ead853952ff74d55c55b9cf7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160027"
---
Delade Image Galleries kan du dela bilder med hjälp av RBAC. Du kan använda RBAC för att dela bilder i din klient, och även till personer utanför din klientorganisation. Men om du vill dela avbildningar utanför din Azure-klient i skala, bör du skapa en appregistrering för att underlätta delning.  Med hjälp av en appregistrering kan möjliggöra mer komplexa delning, t.ex.: 

* Hantera delade bilder när ett företag får en annan och Azure-infrastrukturen fördelas mellan olika klienter. 
* Azure-partner hantera Azure-infrastrukturen åt kunderna. Anpassning av bilder görs i partner-klient, men infrastruktur-distributioner sker på kundens klient. 


## <a name="create-the-app-registration"></a>Skapa appregistreringen

Skapa en programregistrering som används av både klienter för att dela galleriresurser bild.
1. Öppna den [appregistreringar (förhandsversion) i Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Välj **ny registrering** på menyn längst upp på sidan.
1. I **namn**, typ *myGalleryApp*.
1. I **stöds kontotyper**väljer **konton alla organisationskatalog och personliga Microsoft-konton**.
1. I **omdirigerings-URI**, typ *https://www.microsoft.com* och välj sedan **registrera**. När appregistreringen har skapats, öppnas översiktssidan.
1. På översiktssidan kopiera den **(klient)-ID: T** och spara för användning senare.   
1. Välj **certifikat och hemligheter**, och välj sedan **nya klienthemligheten**.
1. I **beskrivning**, typ *apphemlighet för delade bild galleriet mellan klientorganisationer*.
1. I **förfaller**, låt standardvärdet **i 1 år** och välj sedan **Lägg till**.
1. Kopiera värdet för hemligheten och spara den till en säker plats. Du kan inte hämta det när du har lämnat sidan.


Ge appen registrering behörighet att använda delade bildgalleriet.
1. Välj delad galleriet som du vill dela med en annan klient i Azure-portalen.
1. Välj **Välj åtkomstkontroll (IAM)**, och under **Lägg till rolltilldelning** Välj *Lägg till*. 
1. Under **rollen**väljer **läsare**.
1. Under **tilldela åtkomst till:**, lämna det som **Azure AD-användare, grupp eller tjänstens huvudnamn**.
1. Under **Välj**, typ *myGalleryApp* och välja certifikatet när det visas i listan. När du är klar väljer du **spara**.


Skapa ett huvudnamn för tjänsten för klient 2 ska användas för åtkomst till programmet genom att begära en inloggning med en webbläsare. Ersätt *<Tenant2 ID>* med klient-ID för den klient som du vill dela bildgalleriet med. Ersätt *< program (klient)-ID >* med program-ID för registreringen du skapade. När du är klar att göra ersättningarna klistra in URL: en i en webbläsare och följ anvisningarna för inloggning att logga in på klienten 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

I den [Azure-portalen](https://portal.azure.com) logga in som klient 2 och ge appen registrering åtkomst till resursgruppen där du vill skapa den virtuella datorn.

1. Välj resursgruppen och välj sedan **åtkomstkontroll (IAM)**. Under **Lägg till rolltilldelning** Välj **Lägg till**. 
1. Under **rollen**, typ **deltagare**.
1. Under **tilldela åtkomst till:**, lämna det som **Azure AD-användare, grupp eller tjänstens huvudnamn**.
1. Under **Välj** typ *myGalleryApp* välja certifikatet när det visas i listan. När du är klar väljer du **spara**.

> [!NOTE]
> Du måste vänta tills Avbildningsversion helt Slutför som bygger och replikeras innan du kan använda samma avbildning som hanterad för att skapa en annan Avbildningsversion.

