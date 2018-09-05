---
title: När och hur du använder en Azure Multi-Factor Authentication-Provider?
description: När ska du använda en Autentiseringsleverantör med Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 09/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: b601a3d23b23faa16925881a54e2ceba85c800f8
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669073"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>När du ska använda en Azure Multi-Factor Authentication-Provider

Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](howto-mfa-mfasettings.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

Azure Multi-Factor Authentication-Provider används för att dra nytta av funktionerna som tillhandahålls av Azure Multi-Factor Authentication för användare som **inte har licenser**. 

Om du har licenser som omfattar alla användare i din organisation kan behöver du inte en Azure Multi-Factor Authentication-Provider. Skapa en Azure Multi-Factor Authentication-Provider endast om du måste också ange tvåstegsverifiering för vissa användare som inte har licenser.

> [!NOTE]
> Effektiva den 1 September 2018 nya auth-providers kan inte längre skapas. Befintliga autentiseringsleverantörer kan fortsätta att användas och uppdateras. Multifaktorautentisering fortsätter att vara en tillgänglig funktion i Azure AD Premium-licenser.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Varningar relaterade till Azure MFA SDK

En Azure Multi-Factor Auth-provider krävs för att hämta SDK:n. Observera SDK har gjorts inaktuell och stöds inte längre för nya kunder och endast fortsätter att fungera fram till den 14 November 2018. Efter det misslyckas anrop till SDK.

Skapa en Azure Multi-Factor Authentication-Provider för att hämta SDK: N, även om du har Azure MFA-, AAD Premium- eller andra tillsammans licenser. Om du skapar en Azure Multi-Factor Authentication-provider för detta ändamål och redan har licenser, se till att skapa providern med modellen **Per aktiverad användare**. Länka sedan providern till den katalog som innehåller Azure MFA, Azure AD Premium eller andra tillsammans licenser. Den här konfigurationen gör att du bara faktureras om du har fler unika användare som utför tvåstegsverifiering än antalet licenser du äger.

## <a name="what-is-an-mfa-provider"></a>Vad är en MFA-provider?

Om du inte har licenser för Azure Multi-Factor Authentication kan skapa du en autentiseringsleverantör för att kräva tvåstegsverifiering för dina användare.

Det finns två typer av Authentication-providers och skillnaden gäller hur din Azure-prenumeration debiteras. Med alternativet per autentisering räknas antalet autentiseringar som utförs mot din klientorganisation under en månad. Det här alternativet är bäst om du har ett antal användare som bara autentiserar sig ibland. Med alternativet per användare räknas hur många användare i din klientorganisation som utför en tvåstegsverifiering under en månad. Det här alternativet är bäst om du har några användare med licenser men behöver utöka MFA till fler användare utanför licensgränsen.

## <a name="create-an-mfa-provider"></a>Skapa en MFA-provider

Med hjälp av följande steg kan du skapa en Azure Multi-Factor Authentication-provider i Azure-portalen:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Välj **Azure Active Directory** > **MFA Server** > **Leverantörer**.

   ![Leverantörer][Providers]

3. Välj **Lägg till**.
4. Fyll i följande fält och välj sedan **lägg till**:
   - **Namn** – namnet på providern.
   - **Användningsmodell** – Välj ett av två alternativ:
      * Per autentisering – köpmodell som debiterar per autentisering. Används vanligtvis för scenarier som använder Azure Multi-Factor Authentication i ett program som riktar sig till konsumenter.
      * Per aktiverad användare – köpmodell som debiterar per aktiverad användare. Används vanligtvis för medarbetaråtkomst till program som Office 365. Välj det här alternativet om du har några användare som redan har licens för Azure MFA.
   - **Prenumeration** – Den Azure-prenumeration som debiteras för tvåstegsverifieringsaktiviteten hos providern.
   - **Katalog** – Den Azure Active Directory-klient som providern är kopplad till.
      * Du behöver ingen Azure AD-katalog för att skapa en provider. Lämna den här rutan tom om du bara planerar att hämta Azure Multi-Factor Authentication Server.
      * Providern måste vara associerad med en Azure AD-katalog för att du ska kunna dra nytta av de avancerade funktionerna.
      * Det går bara att associera en provider med en Azure AD-katalog.

## <a name="manage-your-mfa-provider"></a>Hantera din MFA-provider

Du kan inte ändra användningsmodellen (per aktiverad användare eller per autentisering) efter att en MFA-provider har skapats. Du kan dock ta bort MFA-providern och sedan skapa en med en annan användningsmodell.

Om den aktuella Multi-Factor Authentication-providern är associerad med en Azure AD-katalog (även kallat en Azure AD-klientorganisation), kan du ta bort MFA-providern och skapa en som är kopplad till samma Azure AD-klientorganisation. Du kan också om du har köpt tillräckligt med licenser för att täcka alla användare som är aktiverade för MFA, du kan ta bort MFA-providern helt och hållet.

Om MFA-providern inte är kopplad till en Azure AD-klientorganisation, eller om du kopplar den nya MFA-providern till en annan Azure AD-klientorganisation, överförs inte användarinställningar eller konfigurationsalternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktivera MFA-servrar för att koppla dem till den nya MFA-providern påverkar inte telefonsamtal och SMS-autentisering, men aviseringar i mobilappen slutar att fungera för alla användare tills de återaktiverar mobilappen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för Multi-Factor Authentication](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "Lägg till MFA-leverantörer"
