---
title: Skapa en Azure AD-klient för Azure Red Hat OpenShift | Microsoft Docs
description: Här är hur du skapar en Azure Active Directory (Azure AD)-klient som värd för ditt Microsoft Azure Red Hat OpenShift-kluster.
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 3c7f0b263cc775b7f9fec62e4d5388250e5124fb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079127"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Skapa en Azure AD-klient för Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift kräver en [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) klient där du vill skapa klustret. En *klient* är en dedikerad instans av Azure AD som en organisation eller app-utvecklare får när de skapar en relation med Microsoft genom att registrera dig för Azure, Microsoft Intune och Microsoft 365. Varje Azure AD-klient skiljer sig och separat från andra Azure AD-klienter och har sitt eget arbete och skola identiteter och app-registreringar.

Om du inte redan har en Azure AD-klient, följer du dessa instruktioner för att skapa en.

## <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Skapa en klient:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) med hjälp av det konto som du vill associera med ditt Azure Red Hat OpenShift-kluster.
2. Öppna den [Azure Active Directory-bladet](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) att skapa en ny klient (kallas även en ny *Azure Active Directory*).
3. Ange en **organisationsnamn**.
4. Ange en **inledande domännamnet**. Detta har *onmicrosoft.com* läggas till den. Du kan återanvända värdet för *organisationsnamn* här.
5. Välj ett land eller region där klienten kommer att skapas.
6. Klicka på **Skapa**.
7. När din Azure AD-klient har skapats kan du välja den **Klicka här för att hantera din nya katalog** länk. Ditt nya klientnamn ska visas i det övre högra hörnet i Azure Portal:  

    ![Skärmbild av portalen som visar innehavarens namn i det övre högra hörnet][tenantcallout]  

8. Anteckna den *klient-ID* så att du senare kan ange var du vill skapa klustret Azure Red Hat OpenShift. Du bör nu se Azure Active Directory-översiktsbladet för den nya klientorganisationen i portalen. Välj **egenskaper** och kopiera värdet för din **katalog-ID**. Vi kommer att referera till det här värdet som den `tenant id` i den [skapa ett kluster i Azure Red Hat OpenShift](tutorial-create-cluster.md) självstudien.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resurser

Kolla in [dokumentation om Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) för mer information om [Azure AD-klienter](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar ett huvudnamn för tjänsten, generera en klient-hemlighet och autentisering Motringnings-URL och skapa en ny Active Directory-användare för att testa appar i Azure Red Hat OpenShift-klustret.

[Skapa en Azure AD app-objekt och en användare](howto-aad-app-configuration.md)