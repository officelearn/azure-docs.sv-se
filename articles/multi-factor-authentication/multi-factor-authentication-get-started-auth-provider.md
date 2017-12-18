---
title: "Komma igång med Azure Multi-Factor Authentication-provider | Microsoft Docs"
description: "Lär dig hur du skapar en Azure Multi-Factor Authentication-provider."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b04fd6f969461cf39016df951007c59047c8857a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Kom igång med en Azure Multi-Factor Authentication-provider
Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](multi-factor-authentication-whats-next.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

En Azure Multi-Factor Authentication-provider används för att dra nytta av funktionerna i den fullständiga versionen av Azure MFA. Den är avsedd för användare som **inte har licenser via Azure MFA, Azure AD Premium eller Enterprise Mobility + Security (EMS)**.  Azure MFA Azure AD Premium och EMS innehåller den fullständiga versionen av Azure MFA som standard. Om du har licenser behöver du inte någon Azure Multi-Factor Authentication-provider.

En Azure Multi-Factor Auth-provider krävs för att hämta SDK:n.

> [!IMPORTANT]
> Utfasningen av Azure Multi-Factor Authentication Software Development Kit (SDK) har meddelats. Den här funktionen stöds inte längre för nya kunder. Kunderna kan fortsätta använda SDK:n till den 14 november 2018. Efter det misslyckas anrop till SDK.

> [!IMPORTANT]
>För att hämta SDK:n måste du skapa en Azure Multi-Factor Auth-provider, även om du har Azure MFA-, AAD Premium- eller EMS-licenser.  Om du skapar en Azure Multi-Factor Authentication-provider för detta ändamål och redan har licenser, se till att skapa providern med modellen **Per aktiverad användare**. Koppla sedan providern till katalogen som innehåller Azure MFA, Azure AD Premium eller EMS-licenser. Den här konfigurationen gör att du bara faktureras om du har fler unika användare som utför tvåstegsverifiering än antalet licenser du äger. 

## <a name="what-is-an-mfa-provider"></a>Vad är en MFA-provider?

Om du inte har licenser för Azure Multi-Factor Authentication kan du skapa en Authentication-provider med tvåstegsverifiering för dina användare.

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

Om den aktuella Multi-Factor Authentication-providern är associerad med en Azure AD-katalog (även kallat en Azure AD-klientorganisation), kan du ta bort MFA-providern och skapa en som är kopplad till samma Azure AD-klientorganisation. Alternativt, om du har köpt tillräckligt många MFA-, Azure AD Premium- eller EMS-licenser (Enterprise Mobility + Security) för att täcka alla användare som är aktiverade för MFA, kan du ta bort MFA-providern helt och hållet.

Om MFA-providern inte är kopplad till en Azure AD-klientorganisation, eller om du kopplar den nya MFA-providern till en annan Azure AD-klientorganisation, överförs inte användarinställningar eller konfigurationsalternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

[Providers]: ./media/multi-factor-authentication-get-started-auth-provider/add-providers.png "Lägg till MFA-leverantörer"
