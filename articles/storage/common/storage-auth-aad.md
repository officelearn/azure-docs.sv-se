---
title: Autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsversion) | Microsoft Docs
description: Autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsversion).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737654"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Autentisera åtkomst till Azure Storage med hjälp av Azure Active Directory (förhandsgranskning)

Azure Storage har stöd för autentisering och auktorisering med Azure Active Directory (AD) för Blob och kön. Med Azure AD kan du använda rollbaserad åtkomstkontroll (RBAC) för att bevilja åtkomst till användare, grupper eller programmet tjänstens huvudnamn. 

Auktorisering av program som har åtkomst till Azure Storage med hjälp av Azure AD ger högre säkerhet och enkel användning över andra alternativ för auktorisering. Du kan fortsätta använda delad nyckel auktorisering med program, kringgår med hjälp av Azure AD behovet att lagra din åtkomstnyckel i koden. På liknande sätt kan du kan fortsätta att använda signaturer för delad åtkomst (SAS) som ger detaljerad tillgång till resurser i ditt lagringskonto, men Azure AD erbjuder liknande funktioner utan att behöva hantera SAS-token eller bekymra dig om att återkalla en komprometterad SAS.

## <a name="about-the-preview"></a>Om förhandsversionen

Tänk på följande om förhandsversionen:

- Azure AD-integrering är tillgänglig för Blob- och köegenskaper tjänster endast i förhandsversionen.
- Azure AD-integrering är tillgänglig för GPv1 och GPv2 Blob storage-konton i alla offentliga regioner. 
- Endast lagringskonton som skapats med Resource Manager-distributionsmodellen stöds. 
- Stöd för anroparen identitetsinformation i Azure Storage Analytics loggning kommer snart.
- Azure AD-tillstånd för åtkomst till resurser i standard storage-konton stöds för närvarande. Tillstånd för åtkomst till sidblobbar i premium storage-konton kommer att stödjas snart.
- Azure Storage har stöd för både inbyggda och anpassade RBAC-roller. Du kan tilldela roller begränsade till prenumerationen, resursgruppen, storage-konto eller en enskild behållare eller kön.
- Azure Storage-klientbibliotek som för närvarande stöder Azure AD-integrering är:
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) (Använd 7.1.x-Preview)
    - Python
        - [BLOB](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [Kön](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> Den här förhandsgranskningen är endast avsedd för icke-produktion användning. Produktion servicenivåavtal (SLA) är inte tillgängliga förrän Azure AD-integrering för Azure Storage har deklarerats allmänt tillgänglig. Om Azure AD-integrering inte stöds ännu för ditt scenario, fortsätta att använda delad nyckel auktorisering eller SAS-token i dina program.
>
> Under förhandsgranskningen inkluderas kan RBAC rolltilldelningar ta upp till fem minuter att sprida.
>
> Azure AD-integrering med Azure Storage kräver att du använder HTTPS för Azure Storage-åtgärder.

## <a name="get-started-with-azure-ad-for-storage"></a>Kom igång med Azure AD för lagring

Det första steget i med hjälp av Azure AD-integrering med Azure Storage är att tilldela RBAC-roller för storage-data till tjänstens huvudnamn (en användare, grupp eller programmet tjänstens huvudnamn) eller hanterade tjänsten identitet (MSI). RBAC-roller omfattar vanliga uppsättningar av behörigheter för behållare och köer. Läs mer om RBAC-roller för Azure Storage i [hantera behörigheter till storage-data med RBAC (förhandsgranskning)](storage-auth-aad-rbac.md).

Om du vill använda Azure AD för att bevilja åtkomst till lagringsresurser i dina program, måste begära en OAuth 2.0-åtkomsttoken från din kod. Information om hur du begär en åtkomst-token och använda den för att tillåta förfrågningar till Azure Storage finns [autentisera med Azure AD från ett Azure Storage-program (förhandsgranskning)](storage-auth-aad-app.md). Om du använder en Azure hanterade tjänsten identitet (MSI), se [autentisera med Azure AD från en Azure VM hanterade tjänstidentiteten (förhandsgranskning)](storage-auth-aad-msi.md).

Azure CLI och PowerShell stöder nu logga in med en Azure AD-identitet. När du har loggat in med en Azure AD identity körs sessionen under identitet. Läs mer i [använder en Azure AD-identitet för åtkomst till Azure Storage med CLI eller PowerShell (förhandsgranskning)](storage-auth-aad-script.md).

## <a name="next-steps"></a>Nästa steg

Ytterligare information om Azure AD-integrering för Azure-Blobbar och köer finns i Azure Storage-teamets blogg inlägg, [om förhandsversionen av Azure AD-autentisering för Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/).
