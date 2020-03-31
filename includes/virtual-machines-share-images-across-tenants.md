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
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903686"
---
Men om du vill dela avbildningar utanför din Azure-klientorganisation bör du i stor skala skapa en appregistrering för att underlätta delning.  Med hjälp av en appregistrering kan mer komplexa delningsscenarier, till exempel: 

* Hantera delade avbildningar när ett företag förvärvar ett annat och Azure-infrastrukturen är spridd över separata klienter. 
* Azure-partner hanterar Azure-infrastruktur på uppdrag av sina kunder. Anpassning av avbildningar görs inom partnerklienten, men infrastrukturdistributionerna sker i kundens klientorganisation. 


## <a name="create-the-app-registration"></a>Skapa appregistreringen

Skapa en programregistrering som ska användas av båda klienterna för att dela bildgalleriresurserna.
1. Öppna [appregistreringarna (förhandsversionen) i Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Välj **Ny registrering** högst upp på sidan.
1. Skriv *myGalleryApp*i **Namn**.
1. I **Kontotyper som stöds**väljer du Konton i alla **organisationskataloger och personliga Microsoft-konton**.
1. Skriv och *https://www.microsoft.com* välj Registrera i Omdirigera **URI**och välj sedan **Registrera**. När appregistreringen har skapats öppnas översiktssidan.
1. På översiktssidan kopierar **du program-ID:t (klient)** och sparar till senare.   
1. Välj **Certifikat & hemligheter**och välj sedan Ny **klienthemlighet**.
1. Skriv Shared *image gallery cross-tenant app secret*i **Beskrivning**.
1. I **Upphör att gälla**lämnar du standardvärdet På ett **år** och väljer sedan Lägg **till**.
1. Kopiera värdet på hemligheten och spara den på en säker plats. Du kan inte hämta den när du har lämnat sidan.


Ge appregistreringen behörighet att använda det delade bildgalleriet.
1. I Azure-portalen väljer du det delade avbildningsgalleriet som du vill dela med en annan klientorganisation.
1. Välj **välj Åtkomstkontroll (IAM)** och under **Lägg till rolltilldelning** väljer du *Lägg till*. 
1. Under **Roll**väljer du **Läsare**.
1. Under **Tilldela åtkomst till:** lämnar du detta som **Azure AD-användare, grupp eller tjänsthuvudnamn**.
1. Under **Välj**skriver du *myGalleryApp* och väljer det när det visas i listan. När du är klar väljer du **Spara**.


## <a name="give-tenant-2-access"></a>Ge klient 2 åtkomst

Ge Tenant 2 åtkomst till programmet genom att begära inloggning med hjälp av en webbläsare. Ersätt * \<Tenant2 ID>* med klient-ID för den klient som du vill dela bildgalleriet med. Ersätt * \<program-ID>* med program-ID:t för den appregistrering som du skapade. När du är klar med att göra ersättningarna klistrar du in webbadressen i en webbläsare och följer inloggningsan uppmaningarna om att logga in på klient 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Logga in som klient 2 i [Azure-portalen](https://portal.azure.com) och ge appregistreringen åtkomst till resursgruppen där du vill skapa den virtuella datorn.

1. Markera resursgruppen och välj sedan **Åtkomstkontroll (IAM)**. Under **Lägg till rolltilldelning** väljer du **Lägg till**. 
1. Skriv **Deltagare**under **Roll**.
1. Under **Tilldela åtkomst till:** lämnar du detta som **Azure AD-användare, grupp eller tjänsthuvudnamn**.
1. Under **Välj** typ *myGalleryApp* väljer du det när det visas i listan. När du är klar väljer du **Spara**.

> [!NOTE]
> Du måste vänta tills avbildningsversionen helt har byggts och replikerats innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.

