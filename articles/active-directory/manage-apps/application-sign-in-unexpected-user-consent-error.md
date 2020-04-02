---
title: Oväntat fel när du gör medgivande till ett program | Microsoft-dokument
description: I artikeln beskrivs fel som kan uppstå under processen att godkänna ett program och vad du kan göra åt dem
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea14e02920cf7ba6c5e0a7b415cb92137c915576
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519710"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Oväntat fel när du gör medgivande till ett program

I den här artikeln beskrivs fel som kan uppstå under processen för att godkänna ett program. Om du felsöker oväntade medgivandemeddelanden som inte innehåller några felmeddelanden läser du [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Många program som integreras med Azure Active Directory kräver behörigheter för att komma åt andra resurser för att fungera. När dessa resurser också är integrerade med Azure Active Directory, begärs behörigheter för att komma åt dem ofta med hjälp av common consent framework. En medgivandefråga visas, som vanligtvis inträffar första gången ett program används men kan också uppstå vid en senare användning av programmet.

Vissa villkor måste vara sanna för att en användare ska kunna godkänna de behörigheter som ett program kräver. Om dessa villkor inte uppfylls kan följande fel uppstå.

## <a name="requesting-not-authorized-permissions-error"></a>Begär inte auktoriserat behörighetsfel
* **AADSTS90093:** &lt;clientAppDisplayName&gt; begär en eller flera behörigheter som du inte har behörighet att bevilja. Kontakta en administratör som kan godkänna det här programmet för din räkning.
* **AADSTS90094:** &lt;clientAppDisplayName&gt; behöver behörighet för att komma åt resurser i organisationen som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

Det här felet uppstår när en användare som inte är företagsadministratör försöker använda ett program som begär behörigheter som bara en administratör kan bevilja. Det här felet kan lösas av en administratör som beviljar åtkomst till programmet på uppdrag av sin organisation.

Det här felet kan också uppstå när en användare hindras från att godkänna ett program på grund av att Microsoft upptäcker att behörighetsbegäran är riskabel. I det här fallet loggas en granskningshändelse också med en kategori av "ApplicationManagement", Aktivitetstyp för "Samtycke till ansökan" och Statusorsaken till "Riskfyllt program upptäckt".

## <a name="policy-prevents-granting-permissions-error"></a>Princip förhindrar att behörighetsfel beviljas
* **AADSTS90093:** En administratör &lt;för tenantDisplayName&gt; har angett en princip &lt;som&gt; hindrar dig från att bevilja namnet på appen de behörigheter som den begär. Kontakta en &lt;administratör för&gt;tenantDisplayName , som kan bevilja behörigheter till den här appen för din räkning.

Det här felet uppstår när en företagsadministratör inaktiverar möjligheten för användare att godkänna program, och sedan försöker en användare som inte är administratör att använda ett program som kräver samtycke. Det här felet kan lösas av en administratör som beviljar åtkomst till programmet på uppdrag av sin organisation.

## <a name="intermittent-problem-error"></a>Återkommande problemfel
* **AADSTS90090:** Det ser ut som inloggningsprocessen stött på ett återkommande problem registrera &lt;de behörigheter&gt;du försökte bevilja till clientAppDisplayName . försök igen senare.

Det här felet indikerar att ett återkommande servicesidans problem har uppstått. Det kan lösas genom att försöka samtycka till programmet igen.

## <a name="resource-not-available-error"></a>Resursen är inte tillgänglig fel
* **AADSTS65005:** AppklientenAppDisplayName &lt;&gt; begärde behörigheter &lt;för åtkomst till&gt; en resursresursAppDisplayName som inte är tillgänglig. 

Kontakta apputvecklaren.

##  <a name="resource-not-available-in-tenant-error"></a>Resursen är inte tillgänglig i klientfel
* **AADSTS65005:** &lt;&gt; clientAppDisplayName begär åtkomst till &lt;en resursresursAppDisplayName&gt; som &lt;inte är&gt;tillgängligt i organisationen TenantDisplayName . 

Kontrollera att den här resursen &lt;är tillgänglig&gt;eller kontakta en administratör för tenantDisplayName .

## <a name="permissions-mismatch-error"></a>Fel i behörigheterna matchar inte
* **AADSTS65005:** Appen begärde samtycke till &lt;åtkomst till&gt;resursresursAppDisplayName . Den här begäran misslyckades eftersom den inte matchar hur appen förkonfigurerades under appregistreringen. Kontakta appleverantören.**

Dessa fel uppstår när programmet en användare försöker samtycka till begär behörigheter för att komma åt ett resursprogram som inte finns i organisationens katalog (klient). Denna situation kan uppstå av flera skäl:

-   Klientprogramutvecklaren har konfigurerat sitt program felaktigt, vilket gör att den begär åtkomst till en ogiltig resurs. I det här fallet måste programutvecklaren uppdatera konfigurationen av klientprogrammet för att lösa problemet.

-   Ett tjänsthuvudnamn som representerar målresursprogrammet finns inte i organisationen eller finns tidigare men har tagits bort. För att lösa problemet måste ett tjänsthuvudnamn för resursprogrammet etableras i organisationen så att klientprogrammet kan begära behörigheter till det. Servicehuvudstaden kan etableras på flera olika sätt, beroende på vilken typ av program det rör sig om, bland annat:

    -   Skaffa en prenumeration för resursprogrammet (Microsoft publicerade program)

    -   Samtycke till resursprogrammet

    -   Bevilja programbehörigheter via Azure-portalen

    -   Lägga till programmet från Azure AD Application Gallery

## <a name="next-steps"></a>Nästa steg 

[Appar, behörigheter och medgivande i Azure Active Directory (v1-slutpunkt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Scope, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkten)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


