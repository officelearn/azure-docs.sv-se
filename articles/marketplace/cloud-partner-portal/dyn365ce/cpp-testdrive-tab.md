---
title: Dynamics 365 för kundengagemang program erbjuder Test Drive fliken | Azure Marketplace
description: Så här konfigurerar du provkörning för en Dynamics 365 för kundengagemang programmet erbjudandet på AppSource-marknadsplatsen.
services: Azure, Marketplace, AppSource, Cloud Partner Portal, Dynamics 365 for Customer Engagement
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 5bb5f39ef5f5bce09a8639ba9eedc6d042e60c1d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942381"
---
# <a name="dynamics-365-for-customer-engagement-application-test-drive-tab"></a>Dynamics 365 för kundengagemang programfliken Test Drive

Använd den **Test Drive** fliken för att skapa en utvärderingsmiljö för dina kunder.  Tjänsten ger kunderna med en praktisk Migreringsprocess provversion av ditt erbjudande viktiga funktioner och fördelar, visas i ett scenario med verkliga implementering.  För utvärderingsversionsalternativ som är tillgängliga är Test Drive de mest effektiva på att skapa högkvalitativa leads och ökad konvertering av dessa leads.  Mer information finns i [vad är en provkörning?](../test-drive/what-is-test-drive.md)

Test Drive-upplevelsen för Dynamics 365-program körs automatiskt som en Microsoft-lösning.  Mer information finns i [värdbaserad Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/hosted-test-drive).

Fliken Test Drive har tre möjliga avsnitt: **Testkör**, **information**, och **teknisk konfiguration**.  De sista två avsnitten visas endast när du har aktiverat Test Drive-funktioner.  En asterisk (*) läggs till fältnamnet anger att det krävs. 


## <a name="test-drive-section"></a>Test Drive-avsnitt

Om du vill aktivera den här funktionen, Välj **Ja** till **aktivera en Test Drive**.


## <a name="details-section"></a>Informationsavsnittet

Du ger grundläggande Test Drive-informationen i den **information** avsnittet.   

![Informationsområde Test Drive](./media/test-drive-tab-details.png)

I följande tabell beskrivs de fält som krävs för att ställa in provkörning för ditt Dynamics 365-program. Required fields are indicted by an asterisk (*).

|      Fält                    |    Beskrivning                  |
|    ---------                  |  ---------------                |
|      Beskrivning\*            |   Beskriv vad du kan göra på din provkörning. Du kan använda grundläggande HTML-taggar för att formatera den här beskrivningen. Till exempel &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;, och rubriker.  |
|  Användarhandbok\*                |   Ladda upp en användarhandbok som dina kunder kan använda för att gå igenom Test Drive-upplevelse. Det här dokumentet måste vara en PDF-fil. |
|  Test Drive demonstrationsvideon (valfritt) |  Du kan ange en videogenomgång av din provkörning. En kund kan den här videon innan de Provkör. Ange en URL till videoklippet på YouTube eller Vimeo. Om du väljer **+ Lägg till Video**, uppmanas du att ange följande information:<ul><li>Namn</li><li>URL</li><li>Miniatyr (i PNG-format, 533 x 324 bildpunkter)</li></ul>  |
|   |   |


## <a name="technical-configuration-section"></a>Teknisk konfigurationsavsnittet

i det här avsnittet får du teknisk information om din provkörning.

![Informationsområde Test Drive](./media/test-drive-tab-tech-config.png)

Där fälten har följande syften.  Required fields are indicted by an asterisk (*).

|      Fält                    |    Beskrivning                  |
|    ---------                  |  ---------------                |
| Typ av Test Drive\*            | Välj **Microsoft värd (Dynamics 365 för kundengagemang)**.  |
| Maxantal samtidiga Provkörningar\*    | Antal samtidiga instanser av en aktiv Test Drive vid en given tidpunkt tid. Varje användare kommer att använda en Dynamics-licens när deras Test Drive är aktiv, så du måste se till att du har minst detta antal Dynamics licenser som är tillgängliga för Test Drive-användare. Rekommenderat värde från 3 till 5.  |
| Driv varaktighet (timmar)\*   | Maximalt antal timmar som användarens Test Drive-instansen kommer att vara aktiv för. När denna tid har överskridits vara instansen avetablerad från din klient. Rekommenderat värde på 2 – 24 timmar beroende på komplexiteten för din app. Användaren kan begära ett annat Test Drive alltid om de körs utanför tid och vill utvärdera igen.  |
| Instans-URL\*                  | URL som Test Drive från början går till. Detta är vanligtvis URL: en för din Dynamics 365-instans som har din app och exempeldata som installeras på.  |
| Azure AD-klient-ID\*            | GUID för Azure-klient för din Dynamics 365-instans. Att hämta det här värdet, logga in på Azure-portalen och gå till **Azure Active Directory** > **Välj egenskaper** > **kopiera katalog-ID**.  |
| Azure AD App-ID\*               | GUID för Azure AD-program  |
| Azure AD App-nyckel\*              | Hemligheten för din Azure AD-program, till exempel: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` |
| Namn på Azure AD-klient\*          | Namnet på Azure-klient för din Dynamics 365-instans. Använd formatet < tenantname. > onmicrosoft.com, till exempel: `testdrive.onmicrosoft.com`  |
| Instans-URL för webb-API\*          | Webb-API-URL för Dynamics 365-instansen. Du kan hämta det här värdet genom att logga in på din Microsoft Dynamics 365-instans och navigera till **inställningar** > **anpassning** > **Developer Resurser** > **instans webb-API (kopiera URL: en)**. Exempelvärde: `https://testdrive.crm.dynamics.com/api/data/v9.0`  |
| Rollnamn\*                     | Namnet på den anpassa Dynamics 365-säkerhetsrollen du har skapat för din provkörning och kommer att tilldelas till användarna när de kör den, till exempel `testdriveuser`. |
|  |  |

När du har angett all nödvändig information, Välj **spara**.


## <a name="next-steps"></a>Nästa steg

Sedan du tillhandahåller information för marknadsföring och försäljning i den [fliken Storefront information](./cpp-storefront-details-tab.md).

