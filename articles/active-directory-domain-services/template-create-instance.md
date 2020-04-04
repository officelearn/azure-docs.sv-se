---
title: Aktivera Azure DS Domain Services med hjälp av en mall | Microsoft-dokument
description: Lär dig hur du konfigurerar och aktiverar Azure Active Directory Domain Services med hjälp av en Azure Resource Manager-mall
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 01/14/2020
ms.author: iainfou
ms.openlocfilehash: b44547998b7ed7159e43bcbbfb4b4456d2a232e9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654557"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Skapa en hanterad Azure Active Directory Domain Services-domän med hjälp av en Azure Resource Manager-mall

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Du använder dessa domäntjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klientorganisation. Med den här integreringen kan användare logga in med sina företagsautentiseringsuppgifter och du kan använda befintliga grupper och användarkonton för att skydda åtkomsten till resurser.

Den här artikeln visar hur du aktiverar Azure AD DS med hjälp av en Azure Resource Manager-mall. Stödresurser skapas med Azure PowerShell.

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande resurser:

* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Se till att du loggar in på din Azure-prenumeration med cmdlet [connect-AzAccount.][Connect-AzAccount]
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure AD PowerShell-modulen och ansluter till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Se till att du loggar in på din Azure AD-klient med hjälp av [Cmdlet Connect-AzureAD.][Connect-AzureAD]
* Du behöver globala administratörsbehörighet i din Azure AD-klient för att aktivera Azure AD DS. *global administrator*
* Du *Contributor* behöver deltagarbehörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

## <a name="dns-naming-requirements"></a>DNS-namngivningskrav

När du skapar en Azure AD DS-instans anger du ett DNS-namn. Det finns några funderingar när du väljer det här DNS-namnet:

* **Inbyggt domännamn:** Som standard används katalogens inbyggda domännamn (ett *.onmicrosoft.com* suffix). Om du vill aktivera säker LDAP-åtkomst till den hanterade domänen via internet kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standarddomänen. Microsoft äger *domänen .onmicrosoft.com,* så att en certifikatutfärdarcertifikatutfärdaren inte utfärdar ett certifikat.
* **Anpassade domännamn:** Den vanligaste metoden är att ange ett anpassat domännamn, vanligtvis ett som du redan äger och är dirigerbart. När du använder en dirigerbar, anpassad domän kan trafiken flöda korrekt efter behov för att stödja dina program.
* **Icke-dirigerbara domänsuffix:** Vi rekommenderar vanligen att du undviker ett suffix som inte är dirigerbart domännamn, till exempel *contoso.local*. Suffixet *.local* är inte dirigerbart och kan orsaka problem med DNS-upplösning.

> [!TIP]
> Om du skapar ett eget domännamn bör du ta hand om befintliga DNS-namnområden. Vi rekommenderar att du använder ett domännamn som är skilt från ett befintligt Azure- eller lokalt DNS-namnutrymme.
>
> Om du till exempel har ett befintligt DNS-namnutrymme *contoso.com*skapar du en Azure AD DS-hanterad domän med det anpassade domännamnet *aaddscontoso.com*. Om du behöver använda säker LDAP måste du registrera dig och äga det här anpassade domännamnet för att generera de certifikat som krävs.
>
> Du kan behöva skapa ytterligare DNS-poster för andra tjänster i din miljö eller villkorliga DNS-vidarebefordrare mellan befintliga DNS-namnutrymmen i din miljö. Om du till exempel kör en webbserver som är värd för en webbplats med rot-DNS-namnet kan det finnas namnkonflikter som kräver ytterligare DNS-poster.
>
> I dessa självstudier och instruktionsartiklar används den anpassade domänen *för aaddscontoso.com* som ett kort exempel. Ange ditt eget domännamn i alla kommandon.

Följande DNS-namnbegränsningar gäller också:

* **Begränsningar för domänprefix:** Du kan inte skapa en hanterad domän med ett prefix som är längre än 15 tecken. Prefixet för det angivna domännamnet (till exempel *aaddscontoso* i *aaddscontoso.com* domännamnet) måste innehålla 15 eller färre tecken.
* **Konflikter i nätverksnamn:** DNS-domännamnet för den hanterade domänen bör inte redan finnas i det virtuella nätverket. Kontrollera särskilt följande scenarier som skulle leda till en namnkonflikt:
    * Om du redan har en Active Directory-domän med samma DNS-domännamn i det virtuella Azure-nätverket.
    * Om det virtuella nätverket där du planerar att aktivera den hanterade domänen har en VPN-anslutning till ditt lokala nätverk. I det här fallet se till att du inte har en domän med samma DNS-domännamn i ditt lokala nätverk.
    * Om du har en befintlig Azure-molntjänst med det namnet i det virtuella Azure-nätverket.

## <a name="create-required-azure-ad-resources"></a>Skapa nödvändiga Azure AD-resurser

Azure AD DS kräver ett tjänsthuvudnamn och en Azure AD-grupp. Med dessa resurser kan Azure AD DS-hanterade domänen synkronisera data och definiera vilka användare som har administratörsbehörighet i den hanterade domänen.

Registrera först resursleverantören Azure AD Domain Services med cmdleten [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Skapa ett Azure AD-tjänsthuvudnamn med hjälp av cmdleten [New-AzureSERVICEPrincipal][New-AzureADServicePrincipal] för Azure AD DS för att kommunicera och autentisera sig själv. Ett specifikt program-ID används med namnet *Domain Controller Services* med ett ID på *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Ändra inte det här program-ID:t.

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Skapa nu en Azure AD-grupp med namnet *AAD DC-administratörer* med hjälp av cmdleten [New-AzureADGroup.][New-AzureADGroup] Användare som läggs till i den här gruppen beviljas sedan behörighet att utföra administrativa uppgifter på azure AD DS-hanterade domänen.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

När gruppen *AAD DC-administratörer* har skapats lägger du till en användare i gruppen med cmdleten [Add-AzureADGroupMember.][Add-AzureADGroupMember] Du får först *AAD DC-administratörer* grupp objekt-ID med hjälp av [Get-AzureADGroup][Get-AzureADGroup] cmdlet, sedan den önskade användarens objekt-ID med hjälp av [Get-AzureADUser][Get-AzureADUser] cmdlet.

I följande exempel är användarobjekt-ID för kontot `admin@aaddscontoso.onmicrosoft.com`med ett UPN i . Ersätt det här användarkontot med UPN för den användare som du vill lägga till i gruppen *AAD DC-administratörer:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

Skapa slutligen en resursgrupp med cmdleten [New-AzResourceGroup.][New-AzResourceGroup] I följande exempel heter resursgruppen *myResourceGroup* och skapas i *regionen westus.* Använd ditt eget namn och önskad region:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Om du väljer en region som stöder tillgänglighetszoner distribueras Azure AD DS-resurserna mellan zoner för ytterligare redundans. Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

Det finns inget för dig att konfigurera för Azure AD DS som ska distribueras över zoner. Azure-plattformen hanterar automatiskt zonfördelningen av resurser. Mer information och om du vill se regionens tillgänglighet finns [i Vad är tillgänglighetszoner i Azure?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Resursdefinition för Azure AD DS

Som en del av Resurshanterarens resursdefinition krävs följande konfigurationsparametrar:

| Parameter               | Värde |
|-------------------------|---------|
| Domännamn              | DNS-domännamnet för den hanterade domänen, med beaktande av tidigare punkter om namngivning av prefix och konflikter. |
| filtreradsynkrasi            | Med Azure AD DS kan du synkronisera *alla* användare och grupper som är tillgängliga i Azure AD, eller en *begränsad* synkronisering av endast specifika grupper. Om du väljer att synkronisera alla användare och grupper kan du inte senare välja att bara utföra en begränsad synkronisering.<br /> Mer information om begränsad synkronisering finns i [Azure AD Domain Services-begränsad synkronisering][scoped-sync].|
| meddelandenInställningar    | Om det finns några aviseringar som genereras i Azure AD DS-hanterad domän kan e-postmeddelanden skickas ut. <br />*Globala administratörer* för Azure-klienten och medlemmar i gruppen *AAD DC-administratörer* kan *aktiveras* för dessa meddelanden.<br /> Om så önskas kan du lägga till ytterligare mottagare för meddelanden när det finns aviseringar som kräver uppmärksamhet.|
| domänKonfigurationTyp | Som standard skapas en Azure AD DS-hanterad domän som en *användarskog.* Den här typen av skog synkroniserar alla objekt från Azure AD, inklusive alla användarkonton som skapats i en lokal AD DS-miljö. Du behöver inte ange ett *domänkonfigureringsvärde* för att skapa en användarskog.<br /> En *resursskog* synkroniserar bara användare och grupper som skapats direkt i Azure AD. Resursskogar är för närvarande i förhandsgranskning. Ange värdet till *ResourceTrusting* för att skapa en resursskog.<br />Mer information *Resource* om resursskogar, inklusive varför du kan använda en och hur du skapar skogsförtroende med lokala AD DS-domäner, finns i [översikt över Azure AD DS-resursskogar][resource-forests].|

Följande definition av komprimerade parametrar visar hur dessa värden deklareras. En användarskog med namnet *aaddscontoso.com* skapas med alla användare från Azure AD synkroniserade till azure AD DS-hanterade domänen:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Följande komprimerade Resource Manager-mallresurstyp används sedan för att definiera och skapa den Azure AD DS-hanterade domänen. Ett virtuellt Azure-nätverk och -undernät måste redan finnas eller skapas som en del av Resource Manager-mallen. Den Azure AD DS-hanterade domänen är ansluten till det här undernätet.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Dessa parametrar och resurstyp kan användas som en del av en bredare Resource Manager-mall för att distribuera en hanterad domän, vilket visas i följande avsnitt.

## <a name="create-a-managed-domain-using-sample-template"></a>Skapa en hanterad domän med exempelmall

Följande fullständiga exempelmall för Resource Manager skapar en Azure AD DS-hanterad domän och de stödda virtuella nätverks-, undernäts- och nätverkssäkerhetsgruppsreglerna. Reglerna för nätverkssäkerhetsgruppen krävs för att skydda den hanterade domänen och se till att trafiken kan flöda korrekt. En användarskog med DNS-namnet *aaddscontoso.com* skapas, med alla användare synkroniserade från Azure AD:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Den här mallen kan distribueras med din önskade distributionsmetod, till exempel [Azure-portalen,][portal-deploy] [Azure PowerShell][powershell-deploy]eller en CI/CD-pipeline. I följande exempel används cmdleten [New-AzResourceGroupDeployment.][New-AzResourceGroupDeployment] Ange namn på en egen resursgrupp och mallfilnamn:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
```

Det tar några minuter att skapa resursen och återställa kontrollen till PowerShell-prompten. Den Hanterade Azure AD DS-domänen fortsätter att etableras i bakgrunden och kan ta upp till en timme att slutföra distributionen. I Azure-portalen visar **översiktssidan** för din Azure AD DS-hanterade domän aktuell status under hela den här distributionsfasen.

När Azure-portalen visar att den Hanterade Azure AD DS-domänen har slutförts måste följande uppgifter slutföras:

* Uppdatera DNS-inställningar för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt konfigurera dessa DNS-inställningar.
* [Aktivera lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändare kan logga in på den hanterade domänen med sina företagsautentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

Om du vill se den hanterade Azure AD DS-domänen i praktiken kan du [domän ansluta till en Windows-vm,][windows-join]konfigurera säker [LDAP][tutorial-ldaps]och [konfigurera synkronisering av lösenord hash][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment
