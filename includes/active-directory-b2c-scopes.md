---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9d394b07ff17ab24305b2580efc8bedfc5e7d384
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183403"
---
#### <a name="applications"></a>[Program](#tab/applications/)

1. Välj **program**.
1. Välj *webapi1* -programmet för att öppna dess **egenskaps** sida.
1. Välj **Publicerade omfång**. Publicerade omfattningar kan användas för att ge ett klient program vissa behörigheter till webb-API: et.
1. För **omfång**anger du `demo.read`och för **beskrivning**anger du `Read access to the web API`.
1. För **omfång**anger du `demo.write`och för **beskrivning**anger du `Write access to the web API`.
1. Välj **Spara**.

#### <a name="app-registrations-preview"></a>[Appregistreringar (för hands version)](#tab/app-reg-preview/)

1. Välj **Appregistreringar (för hands version)** .
1. Välj *webapi1* -programmet för att öppna dess **översikts** sida.
1. Under **Hantera**väljer du **exponera ett API**.
1. Välj länken **mängd** bredvid **program-ID-URI**.
1. Ersätt standardvärdet (ett GUID) med `api`och välj sedan **Spara**. Den fullständiga URI: n visas och ska ha formatet `https://your-tenant-name.onmicrosoft.com/api`. När ditt webb program begär en åtkomsttoken för API: et ska den lägga till denna URI som prefix för varje omfång som du definierar för API: et.
1. Under **omfattningar som definieras av detta API**väljer du **Lägg till ett omfång**.
1. Ange följande värden för att skapa ett omfång som definierar Läs åtkomst till API: et och välj sedan **Lägg till omfång**:
    1. **Omfattnings namn**: `demo.read`
    1. **Visnings namn för administratörs medgivande**: `Read access to demo API`
    1. **Beskrivning av administratörs medgivande**: `Allows read access to the demo API`
1. Välj **Lägg till ett omfång**, ange följande värden för att lägga till ett omfång som definierar skriv åtkomst till API: et och välj sedan **Lägg till omfång**:
    1. **Omfattnings namn**: `demo.write`
    1. **Visnings namn för administratörs medgivande**: `Write access to demo API`
    1. **Beskrivning av administratörs medgivande**: `Allows write access to the demo API`