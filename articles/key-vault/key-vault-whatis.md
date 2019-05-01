---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Lär dig hur Azure Key Vault skyddar kryptografiska nycklar och hemligheter som program och tjänster i molnet använder.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 43794b8ef4e0272362c7695eda75f5af36a77d1e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683465"
---
# <a name="what-is-azure-key-vault"></a>Vad är Azure Key Vault?

Molnbaserade program och tjänster kan du använda kryptografiska nycklar och hemligheter för att hålla informationen säkra. Azure Key Vault skyddar dessa nycklar och hemligheter. När du använder Key Vault kan kryptera du autentiseringsnycklar, lagringskontonycklar, datakrypteringsnycklar, PFX-filer och lösenord med hjälp av nycklar som skyddas av maskinvarusäkerhetsmoduler (HSM).

Key Vault hjälper dig att lösa följande problem:

- **Hemlighetshantering**: Lagra säkert och väl styr åtkomsten till token, lösenord, certifikat, API-nycklar och andra hemligheter.
- **Nyckelhantering**: Skapa och styra krypteringsnycklar som krypterar dina data. 
- **Certifikathantering**: Etablera, hantera och distribuera offentliga och privata Secure Sockets Layer/Transport Layer Security (SSL/TLS)-certifikat för användning med Azure och din interna anslutna resurser. 
- **Store hemligheter som backas upp av HSM: er**: Använda programvara eller FIPS 140-2 Level 2-verifierade HSM: er för att skydda hemligheter och nycklar.

## <a name="basic-concepts"></a>Grundläggande begrepp

Azure Key Vault är ett verktyg för att lagra och komma åt hemligheter på ett säkert sätt. En hemlighet är något som du vill begränsa åtkomst till, till exempel API-nycklar, lösenord eller certifikat. Ett valv är en logisk grupp av hemligheter.

Här följer andra viktiga termer:

- **Klientorganisation**: En klient är den organisation som äger och hanterar en specifik instans av Microsoft-molntjänster. Det används oftast för att referera till uppsättningen med Azure och Office 365-tjänster för en organisation.

- **Vault-ägaren**: Vault-ägaren kan skapa ett nyckelvalv och få fullständig åtkomst och kontroll över den. Valvägaren kan även konfigurera granskning för att logga vem som använder hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera en ny version av nyckeln, säkerhetskopiera den och utföra andra relaterade uppgifter.

- **Valvet konsument**: Valvet konsument kan utföra åtgärder på tillgångar i nyckelvalvet när vault-ägaren beviljar konsument-åtkomst. Vilka åtgärder som är tillgängliga beror på vilka behörigheter som beviljats.

- **Resurs**: En resurs är ett hanterbart objekt som är tillgänglig via Azure. Vanliga exempel är virtuell dator, storage-konto, webbapp, databas och virtuella nätverk. Det finns många fler.

- **Resursgrupp**: En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst.

- **Tjänstens huvudnamn**: Ett huvudnamn för Azure-tjänsten är en säkerhetsidentitet som appar som skapats av användare, tjänster och automatiseringsverktyg använder för att få åtkomst till specifika Azure-resurser. Se det som en ”användaridentitet” (användarnamn och lösenord eller certifikat) med en viss roll och väl kontrollerad behörighet. Ett huvudnamn för tjänsten bör bara behöva utföra vissa åtgärder, till skillnad från en allmän användaridentitet. Det ger bättre säkerhet om beviljas endast den lägsta behörighetsnivån som krävs för att utföra sina administrativa uppgifter.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD är Active Directory-tjänsten för en klient. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation.

- **Azure klient-ID**: En klient-ID är ett unikt sätt att identifiera en Azure AD-instans i en Azure-prenumeration.

- **Hanterade identiteter**: Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Om du använder en hanterad identitet blir lösa problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Mer information finns i följande bild och [översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram över hur hanterade identiteter för Azure-resurser](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Autentisering
För att göra någon åtgärd med Key Vault, måste du först autentisera till den. Det finns tre sätt att autentisera till Key Vault:

- [Hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): När du distribuerar en app på en virtuell dator i Azure kan tilldela du en identitet till din virtuella dator som har åtkomst till Key Vault. Du kan också tilldela identiteter som [andra Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Fördelen med den här metoden är att appen eller tjänsten inte är hanterar rotation för den första hemligheten. Azure roterar automatiskt identiteten. Vi rekommenderar den här metoden som bästa praxis. 
- **Tjänsten huvudnamn och certifikatet**: Du kan använda ett huvudnamn för tjänsten och en tillhörande certifikat som har åtkomst till Key Vault. Vi rekommenderar inte den här metoden eftersom programmets ägare eller utvecklare måste rotera certifikatet.
- **Tjänstens huvudnamn och hemlighet**: Men du kan använda ett huvudnamn för tjänsten och en hemlighet för att autentisera till Key Vault, rekommenderas det inte. Det är svårt att rotera automatiskt bootstrap hemligheten som används för att autentisera till Key Vault.


## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Roll | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |”Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering. Men jag vill dessa nycklar ska vara externa från mitt program så att lösningen passar ett geografiskt distribuerat program. <br/><br/>Jag vill att de här nycklarna och hemligheterna ska skyddas utan att behöva skriva koden själv. Jag vill också att nycklarna och hemligheterna ska vara lätta för mig att använda från mina program med optimala prestanda." |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler.<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Denna metod ger bättre tillförlitlighet och kortare svarstider än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |”Jag vill inte ha ansvaret för mina kunders klientnycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera mig på att göra det jag gör bäst, dvs. erbjuda kärnprogramfunktioner.” |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program behöver utföra kryptografiska åtgärder med hjälp av kunders nycklar, detta Key Vault åt programmet. Programmet kan inte se kundernas nycklar. |
| Säkerhetschef |”Jag vill vara säker på att våra program uppfyller kraven för HSM-moduler med FIPS 140-2 Level 2 för säker nyckelhantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen. <br/><br/>Och även om vi använder flera Azure-tjänster och Azure-resurser vill jag hantera nycklarna från en enda plats i Azure.” |√ HSM-modulerna är FIPS 140-2 Level 2-verifierade.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/><br/>√ Nyckelanvändningen loggas i nära realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault hjälper utvecklare och säkerhetsadministratörer, kan de implementeras och hanteras av en organisations administratör som hanterar andra Azure-tjänster. Den här administratören kan logga in med en Azure-prenumeration, till exempel skapa ett valv för organisationen där du vill lagra nycklar och sedan utföra olika driftåtgärder som dessa:

- Skapa eller importera en nyckel eller hemlighet.
- Återkalla eller ta bort en nyckel eller hemlighet.
- Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
- Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
- Övervaka nyckelanvändningen.

Den här administratören kan sedan utvecklare URI: er för att anropa från sina program. Den här administratören ger också nyckelanvändning loggningsinformation till säkerhetsadministratören. 

![Översikt över hur Azure Key Vault fungerar][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](key-vault-developers-guide.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skydda ditt valv](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
