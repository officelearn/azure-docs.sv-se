---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Genom att använda Azure Key Vault kan kunder kryptera nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/02/2018
ms.author: barclayn
ms.openlocfilehash: 08331a399044eba17060d15f24af1863df38caf5
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480261"
---
# <a name="what-is-azure-key-vault"></a>Vad är Azure Key Vault?

Azure Key Vault hjälper dig att skydda krypteringsnycklar och hemligheter som används av molnprogram och molntjänster. Med Key Vault kan du kryptera nycklar och hemligheter (till exempel autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord) med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM). För ytterligare säkerhet kan du importera eller generera nycklar i HSM-moduler. Om du väljer att göra det bearbetar Microsoft dina nycklar i FIPS 140-2 Level 2-verifierade HSM-moduler (maskinvara och inbyggd programvara).  

Key Vault förenklar nyckelhanteringen och låter dig behålla kontrollen över nycklar som kommer åt och krypterar data. Utvecklare kan skapa nycklar för utveckling och testning på några minuter och sedan sömlöst migrera dem till produktionsnycklar. Säkerhetsadministratörer kan bevilja (och återkalla) behörighet till nycklar efter behov.

## <a name="basic-concepts"></a>Grundläggande begrepp

Azure Key Vault är ett verktyg för att lagra och komma åt hemligheter på ett säkert sätt. En hemlighet är något som du vill begränsa åtkomst till, till exempel API-nycklar, lösenord eller certifikat.
Här följer några viktiga begrepp:
- **Klientorganisation** – en klientorganisation är den organisation som äger och hanterar en specifik instans av Microsoft-molntjänster. Den används oftast på ett exakt sätt för att referera till uppsättningen med Azure- och Office 365-tjänster för en organisation
- **Valvägare** – kan skapa ett nyckelvalv och får fullständig åtkomst och kontroll över det. Valvägaren kan även konfigurera granskning för att logga vem som kommer åt hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera till en ny version av nyckeln, säkerhetskopiera den osv.
- **Resurs** – ett hanterbart objekt som är tillgängligt via Azure. Exempel på vanliga resurser är virtuella datorer, lagringskonton, webbappar, databaser och virtuella nätverk, men det finns många fler.
- **Resursgrupp** – en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Mer information finns i Resursgrupper.
- **Valvkonsument** – kan utföra åtgärder på tillgångarna i nyckelvalvet när valvägaren ger personen åtkomst som är beroende av de behörigheter som beviljas.
- **[Azure Active Directory](../active-directory/active-directory-whatis.md)** är Azure AD-tjänsten för en viss klientorganisation. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation. 
- **Azure-klientorganisations-ID** – detta är ett unikt sätt att identifiera en Azure Active Directory inom en Azure-prenumeration. 
- **Hanterad tjänstidentitet** – Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Hanterad tjänstidentitet (MSI) löser detta problem på ett enklare sätt genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Läs mer om MSI [här](../active-directory/managed-service-identity/overview.md)

    ![MSI-bild](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Roll | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |”Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering, men jag vill att de ska vara externa från mitt program så att lösningen passar ett geografiskt distribuerat program. <br/><br/>Jag vill dessa nycklar och hemligheter ska skyddas utan att behöva skriva koden själv. Jag vill också att nycklarna och hemligheterna ska vara lätta för mig att använda från mina program med optimala prestanda." |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler (HSM).<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Det ger bättre tillförlitlighet och kortare svarstid än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |”Jag vill inte ha ansvaret för mina kunders klientnycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera mig på att göra vad jag gör bäst, dvs. att erbjuda grundläggande funktioner.” |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program behöver utföra kryptografiska åtgärder med hjälp av sina kunders nycklar gör Key Vault detta åt programmet. Programmet kan inte se kundernas nycklar. |
| Säkerhetschef |”Jag vill vara säker på att våra program uppfyller kraven för HSM-moduler med FIPS 140-2 Level 2 för säker nyckelhantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och att vi kan övervaka nyckelanvändningen. <br/><br/>Och även om vi använder flera Azure-tjänster och Azure-resurser vill jag hantera nycklarna från en enda plats i Azure.” |√ HSM-modulerna är FIPS 140-2 Level 2-verifierade.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/><br/>√ Loggningen av nyckelanvändningen sker praktiskt taget i realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Key Vault hjälper utvecklare och säkerhetsadministratörer, men kan även implementeras och hanteras av en administratör som hanterar andra Azure-tjänster för en organisation. Den här administratören kan till exempel logga in med en Azure-prenumeration, skapa ett valv för organisationen där nycklar kan lagras och sedan utföra olika driftåtgärder, t.ex.:

* Skapa eller importera en nyckel eller hemlighet.
* Återkalla eller ta bort en nyckel eller hemlighet.
* Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
* Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
* Övervaka nyckelanvändningen.

Administratören kan sedan ge utvecklare URI:er som de kan anropa från sina program, och förse säkerhetsadministratören med logginformation om nyckelanvändningen. 

   ![Översikt över Azure Key Vault][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](key-vault-developers-guide.md).

## <a name="next-steps"></a>Nästa steg

En Komma igång-självstudiekurs för administratörer finns i [Komma igång med Azure Key Vault](key-vault-get-started.md).

Mer information om av användningsloggning för Key Vault finns i avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [About Keys, Secrets, and Certificates](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx) (Om nycklar, hemligheter och certifikat).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
