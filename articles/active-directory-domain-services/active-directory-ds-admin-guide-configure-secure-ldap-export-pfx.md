---
title: "Konfigurera säker LDAP (LDAPS) i Azure AD Domain Services | Microsoft Docs"
description: "Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: 4d495fcef89a68ca544100dcea8e6c55d4709ae0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurera säker LDAP (LDAPS) för en Azure AD Domain Services-hanterad domän

## <a name="before-you-begin"></a>Innan du börjar
Se till att du har slutfört [uppgift 1 – skaffa ett certifikat för säker LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Uppgift 2 – exportera säker LDAP-certifikatet till en. PFX-fil
Innan du börjar den här uppgiften, se till att du har fått säker LDAP-certifikatet från en offentlig certifikatutfärdare har skapat ett självsignerat certifikat.

Utför följande steg om du vill exportera certifikatet LDAPS till en. PFX-filen.

1. Tryck på den **starta** knappen och skriv **R**. I den **kör** dialogrutan, ange **mmc** och på **OK**.

    ![Starta MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. På den **User Account Control** fråga klickar du på **Ja** att starta MMC (Microsoft Management Console) som administratör.
3. Från den **filen** -menyn klickar du på **Lägg till/ta bort snapin-modulen...** .

    ![Lägg till snapin-modulen MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. I den **lägga till eller ta bort snapin-moduler** markerar den **certifikat** snapin-modulen och klicka på den **Lägg till >** knappen.

    ![Lägga till snapin-modulen certifikat i MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. I den **snapin-modulen Certifikat** väljer **datorkonto** och på **nästa**.

    ![Lägga till snapin-modulen certifikat för datorkontot](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. På den **Välj dator** väljer **lokal dator: (datorn som den här konsolen körs på)** och på **Slutför**.

    ![Lägga till snapin-modulen Certifikat - Välj dator](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. I den **lägga till eller ta bort snapin-moduler** dialogrutan klickar du på **OK** lägga till snapin-modulen certifikat i MMC.

    ![Lägga till snapin-modulen certifikat i MMC - klar](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. I MMC-fönstret klickar du på om du vill expandera **Konsolrot**. Du bör se snapin-modulen Certifikat lästes in. Klicka på **certifikat (lokal dator)** att expandera. Klicka om du vill expandera den **personliga** nod, följt av den **certifikat** nod.

    ![Öppna personliga certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Du bör se det självsignerade certifikatet som vi skapade. Du kan kontrollera egenskaperna för certifikat för att kontrollera certifikatets tumavtryck matchar som rapporterats för windows PowerShell när du skapade certifikatet.
10. Välj det självsignerade certifikatet och **Högerklicka**. Snabbmenyn, Välj **alla aktiviteter** och välj **exportera...** .

    ![Exportera certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. I den **guiden Exportera certifikat**, klickar du på **nästa**.

    ![Exportera certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. På den **Exportera privat nyckel** väljer **Ja, exportera den privata nyckeln**, och klicka på **nästa**.

    ![Exportera certifikatets privata nyckel](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Du måste exportera den privata nyckeln tillsammans med certifikatet. Aktivera säker LDAP för din hanterade domän misslyckas om du anger en PFX som inte innehåller den privata nyckeln för certifikatet.
    >
    >
13. På den **filformat för Export** väljer **Personal Information Exchange – PKCS #12 (. PFX)** som filformat för det exporterade certifikatet.

    ![Exportfilformat för certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Endast den. PFX-filformatet stöds. Exportera inte certifikatet till den. CER-filformat.
    >
    >
14. På den **säkerhet** väljer den **lösenord** alternativet och anger ett lösenord för att skydda den. PFX-filen. Kom ihåg detta lösenord eftersom det krävs i nästa uppgift. Klicka på **nästa** att fortsätta.

    ![Lösenordet för certifikatexport ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Anteckna det här lösenordet. Du behöver det för att aktivera säker LDAP för den här hanterade domänen i [uppgift 3 – aktivera säker LDAP för den hanterade domänen](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. På den **fil som ska exporteras** anger du filnamnet och platsen där du vill exportera certifikatet.

    ![Sökväg för certifikatexport](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. På sidan följande **Slutför** att exportera certifikatet till en PFX-fil. Du bör se bekräftelsedialogrutan när certifikatet har exporterats.

    ![Exportera certifikat utfärdat](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Nästa steg
[Uppgift 3 – aktivera säker LDAP för den hanterade domänen](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
