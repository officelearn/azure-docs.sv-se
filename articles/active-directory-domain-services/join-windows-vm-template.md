---
title: Använda en mall för att ansluta en Windows VM till Azure AD DS | Microsoft-dokument
description: Lär dig hur du använder Azure Resource Manager-mallar för att ansluta till en ny eller befintlig Virtuell Windows Server-dator till en hanterad Azure Active Directory Domain Services-hanterad domän.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: d2108b4c6b81675e2df6789d412dbd7d36f58a4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655116"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Ansluta till en virtuell Windows Server-dator med en hanterad Azure Active Directory Domain Services-domän med hjälp av en Resource Manager-mall

Om du vill automatisera distributionen och konfigurationen av virtuella Azure-datorer kan du använda en Resource Manager-mall. Med dessa mallar kan du skapa konsekventa distributioner varje gång. Tillägg kan också inkluderas i mallar för att automatiskt konfigurera en virtuell dator som en del av distributionen. Ett användbart tillägg ansluter virtuella datorer till en domän, som kan användas med Azure Active Directory Domain Services (Azure AD DS) hanterade domäner.

I den här artikeln visas hur du skapar och ansluter till en virtuell Windows Server-dator till en Azure AD DS-hanterad domän med Hjälp av Resource Manager-mallar. Du lär dig också hur du ansluter en befintlig Virtuell Windows Server till en Azure AD DS-domän.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande resurser och privilegier:

* En aktiv Azure-prenumeration.
    * Om du inte har en Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure Active Directory-klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog med endast molnet.
    * Om det behövs [skapar du en Azure Active Directory-klientorganisation][create-azure-ad-tenant] eller [associerar en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En hanterad Azure Active Directory Domain Services-domän aktiverad och konfigurerad i din Azure AD-klientorganisation.
    * Om det behövs skapar och konfigurerar den första självstudien [en Azure Active Directory Domain Services-instans][create-azure-ad-ds-instance].
* Ett användarkonto som är en del av Azure AD DS-hanterad domän.

## <a name="azure-resource-manager-template-overview"></a>Översikt över Azure Resource Manager-mall

Med Resource Manager-mallar kan du definiera Azure-infrastruktur i kod. De resurser, nätverksanslutningar som krävs eller konfigurationen av virtuella datorer kan alla definieras i en mall. Dessa mallar skapar konsekventa, reproducerbara distributioner varje gång och kan versionsutredas när du gör ändringar. Mer information finns i [översikt över Azure Resource Manager-mallar][template-overview].

Varje resurs definieras i en mall med JavaScript Object Notation (JSON). I följande JSON-exempel används resurstypen *Microsoft.Compute/virtualMachines/extensions* för att installera tillägget Active Directory-domänanslutning. Parametrar används som du anger vid distributionen. När tillägget distribueras ansluts den virtuella datorn till den angivna Azure AD DS-hanterade domänen.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Det här VM-tillägget kan distribueras även om du inte skapar en virtuell dator i samma mall. Exemplen i den här artikeln visar båda följande metoder:

* [Skapa en virtuell Windows Server-dator och gå med i en hanterad domän](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Ansluta till en befintlig virtuell Windows Server-dator till en hanterad domän](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Skapa en virtuell Windows Server-dator och gå med i en hanterad domän

Om du behöver en virtuell Windows Server-dator kan du skapa och konfigurera en med hjälp av en Resource Manager-mall. När den virtuella datorn distribueras installeras ett tillägg för att ansluta den virtuella datorn till en Azure AD DS-hanterad domän. Om du redan har en virtuell dator som du vill gå med i en Azure AD DS-hanterad domän går du till [Gå med i en befintlig Virtuell Dator för Windows Server till en hanterad domän](#join-an-existing-windows-server-vm-to-a-managed-domain).

Så här skapar du en virtuell Windows Server-dator och ansluter den till en Azure AD DS-hanterad domän:

1. Bläddra till [snabbstartsmallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Välj alternativet **Distribuera till Azure**.
1. På sidan **Anpassad distribution** anger du följande information för att skapa och ansluta till en Virtuell Windows Server-dator till den Hanterade Azure AD DS-domänen:

    | Inställning                   | Värde |
    |---------------------------|-------|
    | Prenumeration              | Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services. |
    | Resursgrupp            | Välj resursgruppen för den virtuella datorn. |
    | Location                  | Välj plats för den virtuella datorn. |
    | Befintligt VNET-namn        | Namnet på det befintliga virtuella nätverket som den virtuella datorn ska anslutas till, till exempel *myVnet*. |
    | Befintligt undernätsnamn      | Namnet på det befintliga virtuella nätverksundernätet, till exempel *Arbetsbelastningar*. |
    | Prefix för DNS-etikett          | Ange ett DNS-namn som ska användas för den virtuella datorn, till exempel *myvm*. |
    | Storlek på virtuell dator                   | Ange en vm-storlek, till exempel *Standard_DS2_v2*. |
    | Domän att gå med            | Azure AD DS-hanterade domännamnet, till exempel *aaddscontoso.com*. |
    | Användarnamn på domänen           | Användarkontot i den Hanterade Azure AD DS-domänen som ska användas för `contosoadmin@aaddscontoso.com`att ansluta den virtuella datorn till den hanterade domänen, till exempel . Det här kontot måste vara en del av azure AD DS-hanterad domän. |
    | Domänlösenord           | Lösenordet för användarkontot som angavs i föregående inställning. |
    | Valfri ou-sökväg          | Den anpassade organisationsenheten som du vill lägga till den virtuella datorn. Om du inte anger något värde för den här parametern läggs den virtuella datorn till i standardenheten *för AAD DC-datorer.* |
    | Användarnamn för VM-administratör         | Ange ett lokalt administratörskonto som ska skapas på den virtuella datorn. |
    | Administratörslösenord för virtuell dator         | Ange ett lokalt administratörslösenord för den virtuella datorn. Skapa ett starkt lokalt administratörslösenord för att skydda mot brute-force-attacker med lösenord. |

1. Granska villkoren, markera sedan rutan för **jag godkänner de villkor som anges ovan**. När du är klar väljer du **Köp** för att skapa och ansluta till den virtuella datorn till azure AD DS-hanterad domän.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Mallparameterfilen begär lösenordet för ett användarkonto som är en del av azure AD DS-hanterad domän. Ange inte värden manuellt i den här filen och lämna dem tillgängliga för filresurser eller andra delade platser.

Det tar några minuter för distributionen att slutföras. När du är klar skapas och ansluts Windows VM till den hanterade Azure AD DS-domänen. Den virtuella datorn kan hanteras eller loggas in med domänkonton.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Ansluta till en befintlig virtuell Windows Server-dator till en hanterad domän

Om du har en befintlig virtuell dator, eller grupp av virtuella datorer, som du vill gå med i en Azure AD DS-hanterad domän, kan du använda en Resource Manager-mall för att bara distribuera vm-tillägget.

Så här ansluter du en befintlig virtuell Windows Server-dator till en Azure AD DS-hanterad domän:

1. Bläddra till [snabbstartsmallen](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Välj alternativet **Distribuera till Azure**.
1. På sidan **Anpassad distribution** anger du följande information för att ansluta till den virtuella datorn till den Hanterade Azure AD DS-domänen:

    | Inställning                   | Värde |
    |---------------------------|-------|
    | Prenumeration              | Välj samma Azure-prenumeration där du har aktiverat Azure AD Domain Services. |
    | Resursgrupp            | Välj resursgruppen med den befintliga virtuella datorn. |
    | Location                  | Välj platsen för den befintliga virtuella datorn. |
    | Lista FÖR virtuella datorer                   | Ange den kommaavgränsade listan över befintliga virtuella datorer som ska anslutas till den hanterade Azure AD DS-domänen, till exempel *myVM1,myVM2*. |
    | Användarnamn för domänkoppling     | Användarkontot i den Hanterade Azure AD DS-domänen som ska användas för `contosoadmin@aaddscontoso.com`att ansluta den virtuella datorn till den hanterade domänen, till exempel . Det här kontot måste vara en del av azure AD DS-hanterad domän. |
    | Användarlösenord för domänanslutning | Lösenordet för användarkontot som angavs i föregående inställning. |
    | Valfri ou-sökväg          | Den anpassade organisationsenheten som du vill lägga till den virtuella datorn. Om du inte anger något värde för den här parametern läggs den virtuella datorn till i standardenheten *för AAD DC-datorer.* |

1. Granska villkoren, markera sedan rutan för **jag godkänner de villkor som anges ovan**. När du är klar väljer du **Köp** för att ansluta till den virtuella datorn till azure AD DS-hanterad domän.

> [!WARNING]
> **Hantera lösenord med försiktighet.**
> Mallparameterfilen begär lösenordet för ett användarkonto som är en del av azure AD DS-hanterad domän. Ange inte värden manuellt i den här filen och lämna dem tillgängliga för filresurser eller andra delade platser.

Det tar en stund innan distributionen slutförs. När du är klar ansluts de angivna virtuella Windows-datorerna till azure AD DS-hanterade domänen och kan hanteras eller loggas in med domänkonton.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde du Azure-portalen för att konfigurera och distribuera resurser med hjälp av mallar. Du kan också distribuera resurser med Resource Manager-mallar med [Azure PowerShell][deploy-powershell] eller [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
