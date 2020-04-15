---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: När ska du använda en Auth-provider med Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf50a8f58978a010fe3d8228ace8579fcf52eb38
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309905"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>När du ska använda en Azure Multi-Factor Autentiseringsprovider

> [!IMPORTANT]
> Från och med den 1 september 2018 kan nya auth-leverantörer inte längre skapas. Befintliga auth-providers kan fortsätta att användas och uppdateras, men migrering är inte längre möjlig. Multifaktorautentisering fortsätter att vara tillgänglig som en funktion i Azure AD Premium-licenser.

Tvåstegsverifiering är tillgängligt som standard för globala administratörer med Azure Active Directory och Office 365-användare. Men om du vill dra nytta av [avancerade funktioner](howto-mfa-mfasettings.md) ska du köpa den fullständiga versionen av Azure Multi-Factor Authentication (MFA).

En Azure Multi-Factor Auth Provider används för att dra nytta av funktioner som tillhandahålls av Azure Multi-Factor Authentication för användare som **inte har licenser**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Varningar relaterade till Azure MFA SDK

Observera att SDK har inaktuella och kommer endast att fortsätta att arbeta fram till den 14 november 2018. Efter det misslyckas anrop till SDK.

## <a name="what-is-an-mfa-provider"></a>Vad är en MFA-provider?

Det finns två typer av Auth-providers och skillnaden är kring hur din Azure-prenumeration debiteras. Med alternativet per autentisering räknas antalet autentiseringar som utförs mot din klientorganisation under en månad. Det här alternativet är bäst om du har ett antal användare som bara autentiserar sig ibland. Med alternativet per användare räknas hur många användare i din klientorganisation som utför en tvåstegsverifiering under en månad. Det här alternativet är bäst om du har några användare med licenser men behöver utöka MFA till fler användare utanför licensgränsen.

## <a name="manage-your-mfa-provider"></a>Hantera din MFA-provider

Du kan inte ändra användningsmodellen (per aktiverad användare eller per autentisering) efter att en MFA-provider har skapats.

Om du har köpt tillräckligt med licenser för att täcka alla användare som är aktiverade för MFA kan du ta bort MFA-leverantören helt och hållet.

Om MFA-providern inte är kopplad till en Azure AD-klientorganisation, eller om du kopplar den nya MFA-providern till en annan Azure AD-klientorganisation, överförs inte användarinställningar eller konfigurationsalternativ. Befintliga Azure MFA-servrar måste också återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som genereras via MFA-providern.

### <a name="removing-an-authentication-provider"></a>Ta bort en autentiseringsprovider

> [!CAUTION]
> Det finns ingen bekräftelse när du tar bort en autentiseringsprovider. Att välja **Ta bort** är en permanent process.

Autentiseringsleverantörer finns i **Azure Portal** > **Azure Active Directory** > **Security** > **MFA** > **Providers**. Klicka på listade leverantörer för att se information och konfigurationer som är associerade med den leverantören.

Innan du tar bort en autentiseringsleverantör bör du ta del av alla anpassade inställningar som konfigurerats i din leverantör. Bestäm vilka inställningar som måste migreras till allmänna MFA-inställningar från din leverantör och slutföra migreringen av dessa inställningar. 

Azure MFA-servrar som är länkade till leverantörer måste återaktiveras med autentiseringsuppgifter som genereras under **Azure Portal** > **Azure Active Directory** > **Security** > **MFA** > **Server-inställningar**. Innan du återaktiverar måste följande `\Program Files\Multi-Factor Authentication Server\Data\` filer tas bort från katalogen på Azure MFA-servrar i din miljö:

- caCert (på nytt)
- Cert
- gruppCACert
- groupKey (gruppNyckel)
- Gruppnamn
- licensNyckel
- pkey (p nyckel)

![Ta bort en auth-provider från Azure-portalen](./media/concept-mfa-authprovider/authentication-provider-removal.png)

När du har bekräftat att alla inställningar har migrerats kan du bläddra till **Azure Portal** > **Azure Active Directory** > **Security** > **MFA** > **Providers** och välja ellipser **...** och välja Ta **bort**.

> [!WARNING]
> Om du tar bort en autentiseringsprovider tas all rapporteringsinformation som är associerad med den providern bort. Du kanske vill spara aktivitetsrapporter innan du tar bort leverantören.

> [!NOTE]
> Användare med äldre versioner av Microsoft Authenticator-appen och Azure MFA Server kan behöva registrera om sin app.

## <a name="next-steps"></a>Nästa steg

[Konfigurera inställningar för Multi-Factor Authentication](howto-mfa-mfasettings.md)
