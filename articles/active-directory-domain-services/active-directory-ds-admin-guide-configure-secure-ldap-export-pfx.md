---
title: Skapa en certifikatet för säkert LDAP för en Azure AD Domain Services hanterar domän | Microsoft Docs
description: Skapa en certifikatet för säkert LDAP för en Azure AD Domain Services hantera domän
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: maheshu
ms.openlocfilehash: 0bccb90029f319805086c12162f4e0eec4939865
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430084"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Skapa en. PFX-filen med certifikatet för säkert LDAP (LDAPS) för en hanterad domän

## <a name="before-you-begin"></a>Innan du börjar
Fullständig [uppgift 1: skaffa ett certifikat för säkert LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Uppgift 2: Exportera certifikatet för säkert LDAP till en. PFX-fil
Innan du börjar den här uppgiften kan du hämta certifikatet för säkert LDAP från en offentlig certifikatutfärdare eller skapa ett självsignerat certifikat.

Exportera LDAPS-certifikat till en. PFX-fil:

1. Tryck på den **starta** knappen och skriv **R**. I den **kör** dialogrutan, skriver du in **mmc** och klicka på **OK**.

    ![Starta MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. På den **User Account Control** uppmanar, klickar du på **Ja** att starta MMC (Microsoft Management Console) som administratör.
3. Från den **filen** -menyn klickar du på **Lägg till/ta bort snapin-modulen...** .

    ![Lägga till snapin-modulen MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. I den **lägga till eller ta bort snapin-moduler** dialogrutan den **certifikat** snapin-modulen och klicka på den **Lägg till >** knappen.

    ![Lägga till snapin-modulen certifikat i MMC-konsolen](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. I den **snapin-modulen Certifikat** väljer **datorkontot** och klicka på **nästa**.

    ![Lägga till snapin-modulen certifikat för datorkontot](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. På den **Välj dator** väljer **lokal dator: (datorn som den här konsolen körs på)** och klicka på **Slutför**.

    ![Lägga till snapin-modulen Certifikat - Välj dator](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. I den **lägga till eller ta bort snapin-moduler** dialogrutan klickar du på **OK** lägga till snapin-modulen certifikat i MMC.

    ![Lägga till snapin-modulen certifikat i MMC - klar](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. I MMC-fönstret klickar du på för att expandera **Konsolrot**. Du bör se snapin-modulen certifikat läses in. Klicka på **certifikat (lokal dator)** att expandera. Klicka om du vill expandera den **personliga** nod, följt av den **certifikat** noden.

    ![Öppna personliga certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Du bör se det självsignerade certifikatet som vi skapade. Du kan kontrollera egenskaperna för certifikat för att verifiera certifikatets tumavtryck matchar som rapporteras i Windows PowerShell när du skapade certifikatet.
10. Välj det självsignerade certifikatet och **högerklickar du på**. Välj snabbmenyn ser **alla uppgifter** och välj **exportera...** .

    ![Exportera certifikatet](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. I den **guiden Exportera certifikat**, klickar du på **nästa**.

    ![Guiden för export-certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. På den **Exportera privat nyckel** väljer **Ja, exportera den privata nyckeln**, och klicka på **nästa**.

    ![Exportera certifikatets privata nyckel](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Du måste exportera den privata nyckeln tillsammans med certifikatet. Om du anger en PFX som inte innehåller den privata nyckeln för certifikatet, misslyckas aktivering av säker LDAP för din hanterade domän.
    >
    >

13. På den **filformat för Export** väljer **Personal Information Exchange – PKCS #12 (. PFX)** som filformat för den exporterade certifikatfilen.

    ![Filformat för export-certifikat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Endast den. PFX-filformatet stöds. Inte exportera certifikatet till den. CER-filformat.
    >
    >

14. På den **Security** väljer den **lösenord** alternativet och ange ett lösenord för att skydda den. PFX-fil. Kom ihåg lösenordet eftersom det krävs i nästa aktivitet. Klicka på **Nästa**.

    ![Lösenordet för certifikatexport ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Anteckna det här lösenordet. Du behöver det vid aktivering av säkert LDAP för den här hanterade domänen i [uppgift 3: aktivera säkert LDAP för den hanterade domänen](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. På den **fil som ska exporteras** anger du filnamnet och platsen där du vill exportera certifikatet.

    ![Sökväg för certifikatexport](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. På sidan följande **Slutför** att exportera certifikatet till en PFX-fil. Du bör se bekräftelsedialogrutan när certifikatet har exporterats.

    ![Exportera certifikatet som är klar](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Nästa steg
[Uppgift 3: aktivera säkert LDAP för den hanterade domänen](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
