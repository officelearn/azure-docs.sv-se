---
title: Min första Python-runbook i Azure Automation
description: Självstudiekurs som beskriver hur du skapar, testar och publicerar en enkel Python-runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 13bb12c2c624bfd50933b624a28145172f521747
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427686"
---
# <a name="my-first-python-runbook"></a>Min första Python-runbook

> [!div class="op_single_selector"]
> - [Grafisk](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Den här självstudien vägleder dig genom skapandet av en [Python-runbook](automation-runbook-types.md#python-runbooks) i Azure Automation. Du börjar med en enkel runbook som du testar och publicerar. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen kan göra du runbooken mer robust genom att lägga till runbook-parametrar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
- En virtuell dator i Azure. Eftersom du ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="create-a-new-runbook"></a>Skapa en ny runbook

Börja med att skapa en enkel runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).<br>

1. Välj **Runbooks** under **PROCESSHANTERING** för att öppna listan med runbook-jobb.
1. Välj **+ Lägg till en runbook** att skapa en ny runbook.
1. Ge runbooken namnet *MyFirstRunbook-Python*.
1. I det här fallet ska du skapa en [Python-runbook](automation-runbook-types.md#python-runbooks) så Välj **Python 2** för **runbooktyp**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i runbooken

Nu får du lägga till ett enkelt kommando för att skriva ut texten ”Hello World”:

```python
print("Hello World!")
```

Klicka på **spara** att spara runbook.

## <a name="test-the-runbook"></a>Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Flyttas till *startar* när ett arbetsobjekt begär jobbet, och sedan *kör* när runbook-jobbet börjar köras.
1. När runbook-jobbet är klart visas dess utdata. I det här fallet bör du se *Hello World*.
1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Den runbook som du har skapat är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion.
När du publicerar en runbook kan du skriva över den befintliga publicerade versionen med utkastversionen.
I detta fall använder behöver du ingen publicerad version ännu eftersom du precis har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att visa runbooken på den **Runbooks** nu den visar en **Redigeringsstatusen** av **publicerad**.
1. Bläddra tillbaka åt höger för att visa fönstret för **MyFirstRunbook-Python**.
   Vi kan använda alternativen längs överkanten för att starta runbooken, visa runbooken, schemalägga den så att den startar senare eller skapa en [webhook](automation-webhooks.md) så att den kan startas via ett HTTP-anrop.
1. Du vill starta runbooken, så klicka på **starta** och klicka sedan på **Ok** när bladet starta Runbook öppnas.
1. Ett Jobbfönster öppnas för runbook-jobbet som du skapade. Du kan stänga det här fönstret, men i det här fallet du lämna det öppet så att du kan titta på jobbets status.
1. Jobbets status visas i **jobbsammanfattning** och överensstämmer med de statusar som du såg när du testade runbooken.
1. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.
1. Stäng utdatafönstret.
1. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbook-jobbet skriver till dem.
1. Stäng fönstret strömmar och fönstret jobb för att återgå till fönstret MyFirstRunbook-Python.
1. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.
1. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser.
Skriptet måste autentisera med autentiseringsuppgifter från ditt Automation-konto för att hantera Azure-resurser. För att hjälpa dig att du kan använda den [Azure Automation-verktyget paketet](https://github.com/azureautomation/azure_automation_utility) att göra det enklare att autentisera och interagera med Azure-resurser.

> [!NOTE]
> Automation-kontot måste ha skapats med funktionen tjänstens huvudnamn för ett kör som-certifikat.
> Om ditt automation-konto inte har skapats med tjänstens huvudnamn, du kan autentisera med hjälp av metoden som beskrivs i [autentisera med Azure Management Libraries för Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate).

1. Öppna textredigeraren genom att klicka på **redigera** i fönstret MyFirstRunbook-Python.

2. Lägg till följande kod för att autentisera till Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Lägg till kod för att skapa Python Compute-klienten och starta den virtuella datorn

Om du vill arbeta med Azure virtuella datorer, skapa en instans av den [Azure Compute-klienten för Python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Använd beräkning klient för att starta den virtuella datorn. Lägg till följande kod i runbooken:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
azure_credential,
  str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start("MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Där _MyResourceGroup_ är namnet på resursgruppen som innehåller den virtuella datorn och _TestVM_ är namnet på den virtuella datorn som du vill starta.

Testa och köra runbook igen för att se att den startar den virtuella datorn.

## <a name="use-input-parameters"></a>Använd indataparametrar

Runbooken använder för närvarande hårdkodade värden för namnen på resursgruppen och den virtuella datorn.
Nu ska vi lägga till kod som hämtar dessa värden från indataparametrar.

Du använder den `sys.argv` variabeln för att hämta parametrarnas värden.
Lägga till följande kod i runbooken direkt efter den andra `import` instruktioner:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Det importerar den `sys` -modulen och skapar två variabler för att lagra namnen på resursgrupp och virtuell dator.
Observera att elementet i argumentlistan, `sys.argv[0]`är namnet på skriptet och är inte indata av användaren.

Nu kan du ändra de två sista raderna i runbook för att använda parametervärdena som indata i stället för hårdkodade värden:

```python
async_vm_start = compute_client.virtual_machines.start(resource_group_name, vm_name)
async_vm_start.wait()
```

När du startar en Python-runbook (antingen på den **Test** sidan eller som en publicerad runbook), kan du ange värden för parametrarna i den **starta Runbook** sidan **parametrar** .

När du har startat anger ett värde i den första rutan en sekund visas, och så vidare, så att du kan ange parametervärden för så många behov.

Värdena som är tillgängliga för skript som den `sys.argv` matris som koden du just lade till.

Ange namnet på resursgruppen som värde för den första parametern och namnet på den virtuella datorn att starta som värde för den andra parametern.

![Ange parametervärden](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klicka på **OK** att starta runbooken. Runbook körs och startar den virtuella datorn som du har angett.

## <a name="next-steps"></a>Nästa steg

- Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
- Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
- Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
- Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
- Läs om hur du utvecklar för Azure med Python i [Azure för Python-utvecklare](https://docs.microsoft.com/python/azure/?view=azure-python)
- Python 2-runbook-exempel finns i [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
