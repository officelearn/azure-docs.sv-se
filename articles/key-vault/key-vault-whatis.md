---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Lär dig hur Azure Key Vault skyddar de kryptografiska nycklar och hemligheter som används av moln program och tjänster.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 2786ec387d528e1593e2687d906060f8a2673a8c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934466"
---
# <a name="what-is-azure-key-vault"></a>Vad är Azure Key Vault?

Moln program och-tjänster använder kryptografiska nycklar och hemligheter för att skydda informationen. Azure Key Vault skyddar dessa nycklar och hemligheter. När du använder Key Vault kan du kryptera nycklar, lagrings konto nycklar, data krypterings nycklar, PFX-filer och lösen ord med hjälp av nycklar som skyddas av HSM: er (Hardware Security modules).

Key Vault hjälper till att lösa följande problem:

- **Hemlig hantering**: Lagra på ett säkert sätt och kontrol lera åtkomsten till token, lösen ord, certifikat, API-nycklar och andra hemligheter.
- **Nyckel hantering**: Skapa och kontrol lera krypterings nycklar som krypterar dina data. 
- **Certifikat hantering**: Etablera, hantera och distribuera offentliga och privata Secure Sockets Layer/Transport Layer Security-certifikat (SSL/TLS) för användning med Azure och dina interna anslutna resurser. 
- **Lagra hemligheter som backas upp av HSM: er**: Använd antingen program vara eller FIPS 140-2 nivå 2-verifierade HSM: er för att skydda hemligheter och nycklar.

## <a name="basic-concepts"></a>Grundläggande begrepp

Azure Key Vault är ett verktyg för att lagra och komma åt hemligheter på ett säkert sätt. En hemlighet är något som du vill begränsa åtkomst till, till exempel API-nycklar, lösenord eller certifikat. Ett valv är en logisk grupp hemligheter.

Här följer andra viktiga villkor:

- **Klientorganisation**: En klient organisation är den organisation som äger och hanterar en speciell instans av Microsofts moln tjänster. Den används oftast för att referera till uppsättningen Azure-och Office 365-tjänster för en organisation.

- **Valv ägare**: En valv ägare kan skapa ett nyckel valv och få fullständig åtkomst och kontroll över det. Valvägaren kan även konfigurera granskning för att logga vem som använder hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera en ny version av nyckeln, säkerhetskopiera den och utföra andra relaterade uppgifter.

- **Valv konsument**: En valv konsument kan utföra åtgärder på till gångarna i nyckel valvet när valv ägaren ger konsument åtkomst. Vilka åtgärder som är tillgängliga beror på vilka behörigheter som beviljats.

- **Resurs**: En resurs är ett hanterbart objekt som är tillgängligt via Azure. Vanliga exempel är virtuella datorer, lagrings konton, webbapp, databaser och virtuella nätverk. Det finns många fler.

- **Resursgrupp**: En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst.

- **Tjänstens huvud namn**: Ett huvud namn för Azure-tjänsten är en säkerhets identitet som användare skapade appar, tjänster och automatiserings verktyg använder för att få åtkomst till särskilda Azure-resurser. Tänk på det som "användar identitet" (användar namn och lösen ord eller certifikat) med en speciell roll och tätt kontrollerade behörigheter. Ett huvudnamn för tjänsten bör bara behöva utföra vissa åtgärder, till skillnad från en allmän användaridentitet. Det förbättrar säkerheten om du endast beviljar den lägsta behörighets nivå som krävs för att utföra dess hanterings uppgifter.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD är Active Directory tjänst för en klient. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation.

- **ID för Azure-klient organisation**: Ett klient-ID är ett unikt sätt att identifiera en Azure AD-instans i en Azure-prenumeration.

- **Hanterade identiteter**: Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Genom att använda en hanterad identitet kan du lösa det här problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Mer information finns i följande bild och [Översikt över hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram över hur hanterade identiteter för Azure-resurser fungerar](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Om du vill utföra några åtgärder med Key Vault måste du först autentisera till den. Det finns tre sätt att autentisera till Key Vault:

- [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md): När du distribuerar en app på en virtuell dator i Azure kan du tilldela en identitet till den virtuella datorn som har åtkomst till Key Vault. Du kan också tilldela identiteter till [andra Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md). Fördelen med den här metoden är att appen eller tjänsten inte hanterar rotationen av den första hemligheten. Azure roterar automatiskt identiteten. Vi rekommenderar den här metoden som bästa praxis. 
- **Tjänstens huvud namn och certifikat**: Du kan använda ett tjänst huvud namn och ett associerat certifikat som har åtkomst till Key Vault. Vi rekommenderar inte den här metoden eftersom program ägaren eller utvecklaren måste rotera certifikatet.
- **Tjänstens huvud namn och hemlighet**: Även om du kan använda ett huvud namn för tjänsten och en hemlighet för att autentisera till Key Vault, rekommenderar vi inte det. Det är svårt att automatiskt rotera start hemligheten som används för att autentisera till Key Vault.


## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Role | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |"Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering. Men jag vill att dessa nycklar ska vara externa från mitt program så att lösningen är lämplig för ett program som är geografiskt distribuerat. <br/><br/>Jag vill att de här nycklarna och hemligheterna ska skyddas utan att behöva skriva koden själv. Jag vill också att nycklarna och hemligheterna ska vara lätta för mig att använda från mina program med optimala prestanda." |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler.<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Denna metod ger bättre tillförlitlighet och kortare svarstider än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |”Jag vill inte ha ansvaret för mina kunders klientnycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera mig på att göra det jag gör bäst, dvs. erbjuda kärnprogramfunktioner.” |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program behöver utföra kryptografiska åtgärder med hjälp av kundernas nycklar, Key Vault utföra dessa åtgärder för programmets räkning. Programmet kan inte se kundernas nycklar. |
| Säkerhetschef |”Jag vill vara säker på att våra program uppfyller kraven för HSM-moduler med FIPS 140-2 Level 2 för säker nyckelhantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen. <br/><br/>Och även om vi använder flera Azure-tjänster och Azure-resurser vill jag hantera nycklarna från en enda plats i Azure.” |√ HSM-modulerna är FIPS 140-2 Level 2-verifierade.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/><br/>√ Nyckelanvändningen loggas i nära realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault fördelar utvecklare och säkerhets administratörer kan de implementeras och hanteras av en organisations administratör som hanterar andra Azure-tjänster. Administratören kan till exempel Logga in med en Azure-prenumeration, skapa ett valv för den organisation där nycklarna ska lagras och sedan ansvara för drift uppgifter som dessa:

- Skapa eller importera en nyckel eller hemlighet.
- Återkalla eller ta bort en nyckel eller hemlighet.
- Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
- Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
- Övervaka nyckelanvändningen.

Den här administratören ger sedan utvecklare URI: er möjlighet att anropa från sina program. Den här administratören ger även loggnings information om nyckel användning till säkerhets administratören. 

![Översikt över hur Azure Key Vault fungerar][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](key-vault-developers-guide.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skyddar ditt valv](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
