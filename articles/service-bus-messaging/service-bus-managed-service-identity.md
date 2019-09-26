---
title: Hanterade identiteter för Azure-resurser med Azure Service Bus | Microsoft Docs
description: Använda hanterade identiteter för Azure-resurser med Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 86721907352f19cc7ed69fba1f1a021dcf1ed1b7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299644"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Azure Service Bus resurser
[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en över flera Azure-funktion som låter dig skapa en säker identitet som är kopplad till distributionen som programkoden körs under. Därefter kan du associera den identiteten med åtkomstkontroll roller som ger anpassade behörigheter för åtkomst till specifika Azure-resurser som programmet behöver.

Med hanterade identiteter hanterar den här identiteten för körning i Azure-plattformen. Du behöver inte att lagra och skydda åtkomstnycklar i din programkod eller konfiguration för identiteten själva, eller för de resurser du behöver för att få åtkomst till. En Service Bus-klient som körs i ett Azure App Service program eller på en virtuell dator med aktiverade hanterade entiteter för Azure-resurser behöver inte hantera SAS-regler och-nycklar eller andra åtkomsttoken. Klient programmet behöver bara slut punkts adressen för Service Bus meddelande namn rymden. När appen ansluter binder Service Bus den hanterade entitetens kontext till klienten i en åtgärd som visas i ett exempel senare i den här artikeln. När den är kopplad till en hanterad identitet kan din Service Bus-klient utföra alla behöriga åtgärder. Auktorisering beviljas genom att associera en hanterad entitet med Service Bus roller. 

## <a name="overview"></a>Översikt
När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till en Service Bus entitet måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token `https://servicebus.azure.net`är.
 1. Därefter skickas token som en del av en begäran till tjänsten Service Bus för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. 

Auktoriserings steget kräver att en eller flera RBAC-roller tilldelas säkerhets objekt. Azure Service Bus tillhandahåller RBAC-roller som omfattar uppsättningar med behörigheter för Service Bus resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om hur du tilldelar RBAC-roller till Azure Service Bus finns i [Inbyggda RBAC-roller för Azure Service Bus](#built-in-rbac-roles-for-azure-service-bus). 

Interna program och webb program som gör förfrågningar till Service Bus kan också auktoriseras med Azure AD. Den här artikeln visar hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Service Bus resurser. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Tilldela RBAC-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll (RBAC)](../role-based-access-control/overview.md). Azure Service Bus definierar en uppsättning inbyggda RBAC-roller som omfattar vanliga uppsättningar med behörigheter som används för att komma åt Service Bus entiteter och du kan även definiera anpassade roller för åtkomst till data.

När en RBAC-roll tilldelas till ett säkerhets objekt i Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen eller Service Bus namn området. Ett säkerhets objekt i Azure AD kan vara en användare, en grupp, ett huvud namn för program tjänsten eller en hanterad identitet för Azure-resurser.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Inbyggda RBAC-roller för Azure Service Bus
För Azure Service Bus är hanteringen av namn rymder och alla relaterade resurser via Azure Portal och Azure Resource Management-API: t redan skyddade med hjälp av RBAC-modellen ( *rollbaserad åtkomst kontroll* ). Azure tillhandahåller de nedan inbyggda RBAC-rollerna för att auktorisera åtkomst till en Service Bus namnrymd:

- [Azure Service Bus data ägare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Aktiverar data åtkomst till Service Bus namnrymd och dess entiteter (köer, ämnen, prenumerationer och filter)
- [Azure Service Bus data avsändare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Använd den här rollen för att ge Send Access till Service Bus namnrymd och dess entiteter.
- [Azure Service Bus data mottagare](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Använd den här rollen för att få åtkomst till Service Bus namnrymd och dess entiteter. 

## <a name="resource-scope"></a>Resurs omfång 
Innan du tilldelar en RBAC-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Service Bus resurser, från och med det smala omfång:

- **Kö**, **ämne**eller **prenumeration**: Roll tilldelningen gäller för den speciella Service Bus entiteten. För närvarande stöder Azure Portal inte tilldelning av användare/grupper/hanterade identiteter till Service Bus RBAC-roller på prenumerations nivå. Här är ett exempel på hur du använder Azure CLI-kommandot: [AZ-Role-tilldelning-Create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) för att tilldela en identitet till en Service Bus RBAC-roll: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus namnrymd**: Roll tilldelningen sträcker sig över hela topologin av Service Bus under namn området och till den konsument grupp som är kopplad till den.
- **Resursgrupp**: Roll tilldelningen gäller för alla Service Bus resurser under resurs gruppen.
- **Prenumeration**: Roll tilldelningen gäller för alla Service Bus resurser i alla resurs grupper i prenumerationen.

> [!NOTE]
> Tänk på att det kan ta upp till fem minuter att sprida RBAC-roll tilldelningar. 

Mer information om hur inbyggda roller definieras finns i [förstå roll definitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade RBAC-roller finns i [skapa anpassade roller för Azure Role-baserade Access Control](../role-based-access-control/custom-roles.md).

## <a name="enable-managed-identities-on-a-vm"></a>Aktivera hanterade identiteter på en virtuell dator
Innan du kan använda hanterade identiteter för Azure-resurser för att auktorisera Service Bus resurser från din virtuella dator måste du först aktivera hanterade identiteter för Azure-resurser på den virtuella datorn. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Bevilja behörighet till en hanterad identitet i Azure AD
Om du vill auktorisera en begäran till Service Bus tjänsten från en hanterad identitet i ditt program måste du först konfigurera rollbaserad åtkomst kontroll (RBAC) inställningar för den hanterade identiteten. Azure Service Bus definierar RBAC-roller som omfattar behörigheter för att skicka och läsa från Service Bus. När RBAC-rollen tilldelas till en hanterad identitet beviljas den hanterade identiteten åtkomst till Service Bus entiteter i lämplig omfattning.

Mer information om hur du tilldelar RBAC-roller finns i [autentisera och auktorisera med Azure Active Directory för åtkomst till Service Bus resurser](authenticate-application.md#built-in-rbac-roles-for-azure-service-bus).

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Använda Service Bus med hanterade identiteter för Azure-resurser
Om du vill använda Service Bus med hanterade identiteter måste du tilldela identiteten rollen och lämplig omfattning. I proceduren i det här avsnittet används ett enkelt program som körs under en hanterad identitet och som har åtkomst till Service Bus resurser.

Här ska vi använda ett exempel på ett webb program som finns i [Azure App Service](https://azure.microsoft.com/services/app-service/). Stegvisa instruktioner för hur du skapar ett webb program finns i [skapa en ASP.net Core webbapp i Azure](../app-service/app-service-web-get-started-dotnet.md)

Följ dessa steg när programmet har skapats: 

1. Gå till **Inställningar** och välj **identitet**. 
1. Välj den **status** som ska **användas.** 
1. Spara inställningen genom att välja **Spara**. 

    ![Hanterad identitet för en webbapp](./media/service-bus-managed-service-identity/identity-web-app.png)

När du har aktiverat den här inställningen skapas en ny tjänst identitet i din Azure Active Directory (Azure AD) och konfigureras i App Service-värden.

Tilldela nu den här tjänst identiteten till en roll i det begärda omfånget i Service Bus-resurser.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Tilldela RBAC-roller med hjälp av Azure Portal
Om du vill tilldela en roll till en Service Bus-namnrymd navigerar du till namn området i Azure Portal. Visa inställningarna för Access Control (IAM) för resursen och följ de här anvisningarna för att hantera roll tilldelningar:

> [!NOTE]
> Följande steg tilldelar en tjänst identitets roll till dina Service Bus namnrum. Du kan följa samma steg för att tilldela en roll i andra omfattningar som stöds (resurs grupp och prenumeration). 
> 
> [Skapa ett namn område för Service Bus meddelande tjänst](service-bus-create-namespace-portal.md) om du inte har något. 

1. I Azure Portal navigerar du till Service Bus namn området och visar **översikten** för namn området. 
1. Välj **Access Control (IAM)** på den vänstra menyn för att Visa inställningar för åtkomst kontroll för Service Bus namn området.
1.  Välj fliken **roll tilldelningar** om du vill se en lista över roll tilldelningar.
3.  Välj **Lägg** till för att lägga till en ny roll.
4.  På sidan **Lägg till roll tilldelning** väljer du de Azure Service Bus roller som du vill tilldela. Sök sedan efter den tjänst identitet som du har registrerat för att tilldela rollen.
    
    ![Sidan Lägg till roll tilldelning](./media/service-bus-managed-service-identity/add-role-assignment-page.png)
5.  Välj **Spara**. Identiteten som du har tilldelat rollen visas i listan under den rollen. Följande bild visar till exempel att tjänst identiteten har Azure Service Bus data ägare.
    
    ![Identitet som har tilldelats en roll](./media/service-bus-managed-service-identity/role-assigned.png)

När du har tilldelat rollen får webb programmet åtkomst till Service Bus entiteter under det definierade omfånget. 

### <a name="run-the-app"></a>Kör appen

Ändra nu standard sidan för det ASP.NET-program som du skapade. Du kan använda webb program koden från [den här GitHub-lagringsplatsen](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

Sidan default. aspx är din landnings sida. Du hittar koden i Default.aspx.cs-filen. Resultatet är ett minimalt webb program med några fält och med knapparna **Skicka** och **ta emot** som ansluter till Service Bus för att antingen skicka eller ta emot meddelanden.

Observera hur [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -objektet initieras. Istället för att använda den för delad åtkomst-Token (SAS)-Tokenleverantören koden skapar en tokenleverantör för den hanterade identitet med den `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` anropa. Det finns därför inga hemligheter att kvarhålla och använda. Flödet av den hanterade identitets kontexten till Service Bus och handskakningen för auktoriseringen hanteras automatiskt av token-providern. Det är en enklare modell än att använda SAS.

När du har gjort dessa ändringar, publicera och köra programmet. Du kan enkelt få rätt publicerings data genom att ladda ned och importera en publicerings profil i Visual Studio:

![Hämta publiceringsprofil](./media/service-bus-managed-service-identity/msi3.png)
 
Om du vill skicka eller ta emot meddelanden anger du namnet på namn området och namnet på entiteten som du skapade. Klicka sedan på antingen **Skicka** eller **ta emot**.


> [!NOTE]
> - Den hanterade identiteten fungerar bara i Azure-miljön, på App Services, i virtuella Azure-datorer och i skalnings uppsättningar. För .NET-program ger Microsoft. Azure. Services. AppAuthentication-biblioteket, som används av Service Bus NuGet-paketet, en abstraktion över det här protokollet och har stöd för en lokal utvecklings miljö. Med det här biblioteket kan du också testa din kod lokalt på din utvecklings dator med ditt användar konto från Visual Studio, Azure CLI 2,0 eller Active Directory integrerad autentisering. Mer information om lokala utvecklings alternativ med det här biblioteket finns i [tjänst-till-tjänst-autentisering för att Azure Key Vault med .net](../key-vault/service-to-service-authentication.md).  
> 
> - För närvarande fungerar inte hanterade identiteter med App Service distributions fack.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
