---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183403"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **program**.
1. Välj *webapi1-programmet* för att öppna sidan **Egenskaper.**
1. Välj **Publicerade omfång**. Publicerade scope kan användas för att ge ett klientprogram vissa behörigheter till webb-API:et.
1. Ange **för** `demo.read`SCOPE och **beskrivning** `Read access to the web API`.
1. Ange **för** `demo.write`SCOPE och **beskrivning** `Write access to the web API`.
1. Välj **Spara**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (förhandsgranskning)**.
1. Välj *webapi1-programmet* för att öppna sidan **Översikt.**
1. Under **Hantera**väljer du **Exponera ett API**.
1. Välj länken **Ange** bredvid **Program-ID.**
1. Ersätt standardvärdet (ett GUID) med `api`och välj sedan **Spara**. Den fullständiga URI visas, och bör `https://your-tenant-name.onmicrosoft.com/api`vara i formatet . När webbprogrammet begär en åtkomsttoken för API:et bör den lägga till den här URI:n som prefix för varje scope som du definierar för API:et.
1. Under **Scope som definieras av det här API:et**väljer du Lägg till ett **scope**.
1. Ange följande värden för att skapa ett scope som definierar läsåtkomst till API:et och välj sedan **Lägg till scope:**
    1. **Namn på omfattning:**`demo.read`
    1. **Visningsnamn för administratörsmedgivande:**`Read access to demo API`
    1. **Beskrivning av administratörssamtycke:**`Allows read access to the demo API`
1. Välj **Lägg till ett scope,** ange följande värden för att lägga till ett scope som definierar skrivåtkomst till API:et och välj sedan Lägg till **scope:**
    1. **Namn på omfattning:**`demo.write`
    1. **Visningsnamn för administratörsmedgivande:**`Write access to demo API`
    1. **Beskrivning av administratörssamtycke:**`Allows write access to the demo API`