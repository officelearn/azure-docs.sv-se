---
title: Skapa en Azure AD-klient för Azure Red Hat OpenShift
description: Så här skapar du en Azure Active Directory-klient (Azure AD) som värd för ditt Microsoft Azure Red Hat OpenShift-kluster.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ad03538cafcce9c1d660d0f2ac5eb3c6ae5f4f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243697"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Skapa en Azure AD-klient för Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift kräver en [Azure Active Directory-klientorganisation (Azure AD)](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) där du kan skapa ditt kluster. En *klient är* en dedikerad instans av Azure AD som en organisation eller apputvecklare får när de skapar en relation med Microsoft genom att registrera dig för Azure, Microsoft Intune eller Microsoft 365. Varje Azure AD-klient är distinkt och separat från andra Azure AD-klienter och har sina egna arbets- och skolidentiteter och appregistreringar.

Om du inte redan har en Azure AD-klient följer du dessa instruktioner för att skapa en.

## <a name="create-a-new-azure-ad-tenant"></a>Skapa en ny Azure AD-klient

Så här skapar du en klient:

1. Logga in på [Azure-portalen](https://portal.azure.com/) med det konto som du vill associera med ditt Azure Red Hat OpenShift-kluster.
2. Öppna [Azure Active Directory-bladet](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) för att skapa en ny klientorganisation (kallas även en ny *Azure Active Directory*).
3. Ange ett **organisationsnamn**.
4. Ange ett **ursprungligt domännamn**. Detta kommer att ha *onmicrosoft.com* bifogas den. Du kan återanvända värdet för *organisationsnamn* här.
5. Välj ett land eller en region där klienten ska skapas.
6. Klicka på **Skapa**.
7. När din Azure AD-klient har skapats väljer du **klicka här för att hantera den nya kataloglänken.** Ditt nya klientnamn ska visas längst upp till höger i Azure-portalen:  

    ![Skärmbild av portalen som visar klientnamnet längst upp till höger][tenantcallout]  

8. Anteckna *klient-ID* så att du senare kan ange var du ska skapa ditt Azure Red Hat OpenShift-kluster. I portalen bör du nu se azure Active Directory-översiktsbladet för din nya klient. Välj **Egenskaper** och kopiera värdet för **ditt katalog-ID**. Vi refererar till `TENANT` det här värdet som i självstudien [Skapa en Azure Red Hat OpenShift-kluster.](tutorial-create-cluster.md)

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Resurser

Mer information om [Azure AD-klienter](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)finns i [Azure Active Directory-dokumentationen](https://docs.microsoft.com/azure/active-directory/) .

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar ett huvudnamn för tjänsten, genererar en klienthemlighet och autentiseringsåteråterlämnings-URL och skapar en ny Active Directory-användare för att testa appar i Ditt Azure Red Hat OpenShift-kluster.

[Skapa ett Azure AD-appobjekt och en användare](howto-aad-app-configuration.md)
