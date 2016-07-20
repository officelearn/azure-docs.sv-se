<properties
   pageTitle="Komma igång med Azure AD Privileged Identity Management | Microsoft Azure"
   description="Lär dig hur du hanterar privilegierade identiteter med programmet Azure Active Directory Privileged Identity Management på Azure-portalen."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Komma igång med Azure Privileged Identity Management


Med Azure Active Directory (AD) Privileged Identity Management kan du hantera, kontrollera och övervaka privilegierade identiteter och åtkomsten till resurser i Azure AD och andra Microsoft-onlinetjänster som Office 365 eller Microsoft Intune.  

Den här artikeln visar hur du lägger till appen Azure AD PIM på instrumentpanelen på Azure-portalen.

## Lägga till programmet Privileged Identity Management

Innan du använder Azure AD Privileged Identity Management måste du lägga till programmet på instrumentpanelen på Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör för din katalog.
2. Om din organisation har mer än en katalog klickar du på ditt användarnamn längst upp till höger på Azure-portalen och väljer den katalog där du ska använda PIM.
3. Välj ikonen **Nytt** i det vänstra navigeringsfönstret.
4. Välj **Säkerhet + identitet**.
5. Välj **Azure AD Privileged Identity Management**.
6. Markera **Fäst på instrumentpanelen** och klicka sedan på **Skapa**. Programmet Privileged Identity Management öppnas.


Om du är den första personen som använder Azure AD Privileged Identity Management i din katalog vägleds du genom din första tilldelningsupplevelse i [säkerhetsguiden](active-directory-privileged-identity-management-security-wizard.md). Därefter blir du automatiskt katalogens första **säkerhetsadministratör** och **administratör av privilegierade roller**. Endast en administratör av privilegierade roller kan komma åt det här programmet för att hantera åtkomsten för andra administratörer.  

Om du i stället har tilldelats en eller flera roller av en annan administratör för privilegierade roller kan du välja vilken roll som ska aktiveras. Om du själv är administratör av privilegierade roller kan du även välja att **hantera identiteter**.  


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nästa steg

[Översikten över Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) innehåller mer information om hur du kan hantera administrativ åtkomst i din organisation.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]



<!--HONumber=Jun16_HO2-->


