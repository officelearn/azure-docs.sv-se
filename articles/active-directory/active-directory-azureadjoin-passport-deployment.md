---
title: "Aktivera Microsoft Windows Hello för företag i din organisation | Microsoft Docs"
description: Stegvisa anvisningar om att aktivera Microsoft Passport i din organisation.
services: active-directory
documentationcenter: 
keywords: "Konfigurera Microsoft Passport, Microsoft Windows Hello för företag-distribution"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 0aa16e3466b36b6d1d83308cf37623aa15d61fcb
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Aktivera Microsoft Windows Hello för företag i din organisation
Efter [ansluta domänanslutna Windows 10-enheter med Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), gör följande för att aktivera Microsoft Windows Hello för företag i din organisation:

1. Distribuera System Center Configuration Manager  
2. Konfigurera principinställningar
3. Konfigurera certifikatprofilen  

## <a name="deploy-system-center-configuration-manager"></a>Distribuera System Center Configuration Manager
För att distribuera certifikat baserat på Windows Hello för företag nycklar, behöver du följande:

* **System Center Configuration Manager Current Branch** -måste du installera versionen 1606 eller bättre. Mer information finns i [dokumentation för System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) och [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Infrastruktur för offentliga nycklar (PKI)** – för att aktivera Microsoft Windows Hello för företag med hjälp av certifikat, du måste ha en PKI på plats. Om du inte har någon, eller om du inte vill använda för användarcertifikat, kan du distribuera en ny domänkontrollant med Windows Server 2016 version 10551 (eller senare) installerat. Följ stegen för att [installerar en replikeringsdomänkontrollant i en befintlig domän](https://technet.microsoft.com/library/jj574134.aspx) eller [installera en ny Active Directory-skog, om du skapar en ny miljö](https://technet.microsoft.com/library/jj574166). (De ISO: er är tillgängliga för hämtning på [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Konfigurera principinställningar
Om du vill konfigurera den Microsoft Windows Hello för företag principinställningar, har du två alternativ:

* Grupprincip i Active Directory 
* System Center Configuration Manager 

Med en Grupprincip i Active Directory är den rekommenderade metoden för att konfigurera Microsoft Windows Hello för företag principinställningar. 

Med System Center Configuration Manager är den bästa metoden när du också använda det för att distribuera certifikat. Det här scenariot:

* Garanterar kompatibilitet med nyare distributionsscenarier
* Kräver på klientsidan Windows 10 Version 1607 eller bättre.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Konfigurera Microsoft Windows Hello för företag via en Grupprincip i Active Directory
**Steg**:

1. Öppna Serverhanteraren och gå till **verktyg** > **Grupprinciphantering**.
2. Från Grupprinciphantering, navigera till domännod som motsvarar den domän där du vill aktivera Azure AD Join.
3. Högerklicka på **grupprincipobjekt**, och välj **ny**. Namnge din grupprincipobjekt, till exempel aktivera Windows Hello för företag. Klicka på **OK**.
4. Högerklicka på din nya grupprincipobjektet och välj sedan **redigera**.
5. Gå till **Datorkonfiguration** > **principer** > **Administrationsmallar** > **Windows Komponenter** > **Windows Hello för företag**.
6. Högerklicka på **aktivera Windows Hello för företag**, och välj sedan **redigera**.
7. Välj den **aktiverad** alternativknapp och klicka sedan på **tillämpa**. Klicka på **OK**.
8. Du kan nu länka grupprincipobjektet till en plats. Om du vill aktivera den här principen för alla domänanslutna Windows 10-enheter i din organisation, länka grupprincipen till domänen. Exempel:
   * En viss organisationsenhet (OU) i Active Directory där Windows 10-domänanslutna datorer kommer att finnas
   * En viss säkerhetsgrupp som innehåller Windows 10-domänanslutna datorer som ska registreras automatiskt med Azure AD

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Konfigurera Windows Hello för företag med System Center Configuration Manager
**Steg**:

1. Öppna den **System Center Configuration Manager**, och gå sedan till **tillgångar och efterlevnad > kompatibilitetsinställningar > åtkomst till företagsresurser > Windows Hello för företag-profiler**.
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. Klicka på i verktygsfältet högst upp **skapa Windows Hello för företag-profil**.
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. På den **allmänna** dialogrutan, utför följande steg:
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. I den **namn** textruta, ange ett namn för din profil, till exempel **min WHfB profil**.
   
    b. Klicka på **Nästa**.
4. På den **plattformar som stöds av** dialogrutan Välj de plattformar som ska etableras med den här Windows Hello för företag-profilen och klickar sedan på **nästa**.
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. På den **inställningar** dialogrutan, utför följande steg:
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Som **konfigurera Windows Hello för företag**väljer **aktiverad**.
   
    b. Som **använder en Trusted Platform Module (TPM)**väljer **krävs**. 
   
    c. Som **autentiseringsmetod**väljer **certifikatbaserad**.
   
    d. Klicka på **Nästa**.
6. På den **sammanfattning** dialogrutan klickar du på **nästa**.
7. På den **slutförande** dialogrutan klickar du på **Stäng**.
8. Klicka på i verktygsfältet högst upp **distribuera**.
   
    ![Konfigurera Windows Hello för företag](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>Konfigurera certifikatprofilen
Om du använder certifikatbaserad autentisering för lokal autentisering måste du konfigurera och distribuera en certifikatprofil. Den här uppgiften måste du ställa in en NDES-servern och Certifikatregistreringsplatsen platsroll i System Center Configuration Manager. Mer information finns i [krav för Certifikatprofiler i Configuration Manager](https://technet.microsoft.com/library/dn261205.aspx).

1. Öppna den **System Center Configuration Manager**, och gå sedan till **tillgångar och efterlevnad > kompatibilitetsinställningar > åtkomst till företagsresurser > Certifikatprofiler**.
2. Välj en mall som har smartkort-inloggning utökad nyckelanvändning (EKU).

På den **SCEP-registrering** sidan av certifikatprofil, måste du välja **installera på Passport for Work, rapportera annars fel** som den **Nyckellagringsprovider**.

## <a name="next-steps"></a>Nästa steg
* [Windows 10 för företaget: Sätt att använda enheter för arbete](active-directory-azureadjoin-windows10-devices-overview.md)
* [Utöka molnkapaciteten till Windows 10-enheter via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autentisera utan lösenord](active-directory-azureadjoin-passport.md)
* [Läs mer om användningsscenarier för Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ansluta domänanslutna enheter till miljöer med Azure AD och Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Konfigurera Azure AD Join](active-directory-azureadjoin-setup.md)

