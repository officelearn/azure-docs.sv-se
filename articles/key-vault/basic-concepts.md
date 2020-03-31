---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Lär dig hur Azure Key Vault skyddar kryptografiska nycklar och hemligheter som molnprogram och tjänster använder.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 03ad504258dd9448753f37402067a0da3e0a2c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197648"
---
# <a name="azure-key-vault-basic-concepts"></a>Grundläggande koncept för Azure Key Vault

Azure Key Vault är ett verktyg för att lagra och komma åt hemligheter på ett säkert sätt. En hemlighet är något som du vill begränsa åtkomst till, till exempel API-nycklar, lösenord eller certifikat. Ett valv är en logisk grupp av hemligheter.

Här är andra viktiga termer:

- **Klientorganisation**: en klientorganisation är den organisation som äger och hanterar en specifik instans av Microsoft-molntjänster. Det används oftast för att referera till uppsättningen Azure- och Office 365-tjänster för en organisation.

- **Valvägare**: valvägaren kan skapa ett nyckelvalv och få fullständig åtkomst till och kontroll över det. Valvägaren kan även konfigurera granskning för att logga vem som använder hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera en ny version av nyckeln, säkerhetskopiera den och utföra andra relaterade uppgifter.

- **Valvkonsument**: valvkonsumenten kan utföra åtgärder på tillgångarna i nyckelvalvet när valvägaren ger konsumenten åtkomst. Vilka åtgärder som är tillgängliga beror på vilka behörigheter som beviljats.

- **Resurs**: en resurs är ett hanterbart objekt som är tillgängligt via Azure. Vanliga exempel är virtuell dator, lagringskonto, webbapp, databas och virtuellt nätverk. Det finns många fler.

- **Resursgrupp**: resursgruppen är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst.

- **Tjänsthuvudnamn**: Ett Azure-tjänsthuvudnamn är en säkerhetsidentitet som användarskapade appar, tjänster och automatiseringsverktyg använder för att komma åt specifika Azure-resurser. Se det som en "användaridentitet" (användarnamn och lösenord eller certifikat) med en viss roll och noggrant kontrollerade behörigheter. Ett huvudnamn för tjänsten bör bara behöva utföra vissa åtgärder, till skillnad från en allmän användaridentitet. Det förbättrar säkerheten om du bara beviljar den minsta behörighetsnivå som den behöver för att utföra sina hanteringsuppgifter.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD är Active Directory-tjänsten för en klientorganisation. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation.

- **ID för Azure-klientorganisation**: detta är ett unikt sätt att identifiera en Azure Active Directory-instans inom en Azure-prenumeration.

- **Hanterade identiteter**: Azure Key Vault är ett sätt att säkert lagra autentiseringsuppgifter och andra nycklar och hemligheter, men din kod måste autentisera till Key Vault för att hämta dem. Om du använder en hanterad identitet blir det enklare att lösa problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Mer information finns i följande avbildning och [översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram över hur hanterade identiteter för Azure-resurser fungerar](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autentisering
Om du vill utföra alla åtgärder med Key Vault måste du först autentisera till det. Det finns tre sätt att autentisera till Key Vault:

- [Hanterade identiteter för Azure-resurser:](../active-directory/managed-identities-azure-resources/overview.md)När du distribuerar en app på en virtuell dator i Azure kan du tilldela en identitet till din virtuella dator som har åtkomst till Key Vault. Du kan också tilldela identiteter till [andra Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Fördelen med den här metoden är att appen eller tjänsten inte hanterar rotationen av den första hemligheten. Azure roterar automatiskt identiteten. Vi rekommenderar den här metoden som bästa praxis. 
- **Tjänstens huvudnamn och certifikat**: Du kan använda ett tjänsthuvudnamn och ett associerat certifikat som har åtkomst till Key Vault. Vi rekommenderar inte den här metoden eftersom programägaren eller utvecklaren måste rotera certifikatet.
- **Tjänstens huvudnamn och hemlighet:** Även om du kan använda ett tjänsthuvudnamn och en hemlighet för att autentisera till Key Vault, rekommenderar vi inte det. Det är svårt att automatiskt rotera bootstrap hemlighet som används för att autentisera till Key Vault.


## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Roll | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |"Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering. Men jag vill att dessa nycklar ska vara externa från mitt program så att lösningen är lämplig för ett program som är geografiskt distribuerat. <br/><br/>Jag vill att de här nycklarna och hemligheterna ska skyddas utan att behöva skriva koden själv. Jag vill också att nycklarna och hemligheterna ska vara lätta för mig att använda från mina program med optimala prestanda." |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler.<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Denna metod ger bättre tillförlitlighet och kortare svarstider än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |”Jag vill inte ha ansvaret för mina kunders klientnycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera mig på att göra det jag gör bäst, dvs. erbjuda kärnprogramfunktioner.” |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program måste utföra kryptografiska åtgärder med hjälp av kundernas nycklar utför Key Vault dessa åtgärder för programmets räkning. Programmet kan inte se kundernas nycklar. |
| Säkerhetschef |”Jag vill vara säker på att våra program uppfyller kraven för HSM-moduler med FIPS 140-2 Level 2 för säker nyckelhantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen. <br/><br/>Och även om vi använder flera Azure-tjänster och Azure-resurser vill jag hantera nycklarna från en enda plats i Azure.” |√ HSM-modulerna är FIPS 140-2 Level 2-verifierade.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/><br/>√ Nyckelanvändningen loggas i nära realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault gynnar utvecklare och säkerhetsadministratörer kan det implementeras och hanteras av en organisations administratör som hanterar andra Azure-tjänster. Den här administratören kan till exempel logga in med en Azure-prenumeration, skapa ett valv för den organisation där nycklar ska lagras och sedan ansvara för operativa uppgifter som dessa:

- Skapa eller importera en nyckel eller hemlighet.
- Återkalla eller ta bort en nyckel eller hemlighet.
- Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
- Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
- Övervaka nyckelanvändningen.

Den här administratören ger sedan utvecklare URI:er att anropa från sina program. Den här administratören ger också viktig information om användningsloggning till säkerhetsadministratören. 

![Översikt över hur Azure Key Vault fungerar][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](key-vault-developers-guide.md).

## <a name="next-steps"></a>Nästa steg

Läs om hur du [säkrar ditt valv.](key-vault-secure-your-key-vault.md)

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
