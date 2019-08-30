---
title: Min första python-Runbook i Azure Automation
description: Självstudie som vägleder dig genom att skapa, testa och publicera en enkel python-Runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6808ad101a828708ff4fe77bb4b8a55cf8c5fec7
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135439"
---
# <a name="my-first-python-runbook"></a>Min första python-Runbook

> [!div class="op_single_selector"]
> - [Grafisk](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Den här självstudien vägleder dig genom skapandet av en [python-Runbook](automation-runbook-types.md#python-runbooks) i Azure Automation. Du börjar med en enkel Runbook som du testar och publicerar. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen gör du runbooken mer robust genom att lägga till Runbook-parametrar.

> [!NOTE]
> Det finns inte stöd för att använda en webhook för att starta en python-Runbook.

## <a name="prerequisites"></a>Förutsättningar

För att kunna genomföra den här kursen behöver du följande:

- En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
- En virtuell dator i Azure. Eftersom du ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="create-a-new-runbook"></a>Skapa en ny Runbook

Du börjar med att skapa en enkel Runbook som visar texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).<br>

1. Välj **Runbooks** under **PROCESSHANTERING** för att öppna listan med runbook-jobb.
1. Välj **+ Lägg till en Runbook** för att skapa en ny Runbook.
1. Ge runbooken namnet *MyFirstRunbook-python*.
1. I det här fallet ska du skapa en python- [Runbook](automation-runbook-types.md#python-runbooks) så att du väljer **python 2** för **Runbook-typ**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägg till kod i Runbook

Nu lägger du till ett enkelt kommando för att skriva ut texten "Hello World":

```python
print("Hello World!")
```

Klicka på **Spara** för att spara runbooken.

## <a name="test-the-runbook"></a>Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Den flyttar till *Start* när en arbets tagare anlitar jobbet och *Kör* sedan när runbooken faktiskt börjar köras.
1. När runbook-jobbet är klart visas dess utdata. I så fall bör du se *Hello World*.
1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Den Runbook som du har skapat är fortfarande i utkast läge. Du måste publicera den innan du kan köra den i produktion.
När du publicerar en Runbook skriver du över den befintliga publicerade versionen med utkastet.
I det här fallet har du inte någon publicerad version än eftersom du nyss skapade runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du rullar åt vänster för att Visa Runbook i fönstret **Runbooks** nu visas **redigerings statusen** **publicerad**.
1. Rulla tillbaka till höger för att visa fönstret för **MyFirstRunbook-python**.
   Med alternativen överst kan vi starta runbooken, Visa runbooken eller schemalägga den så att den startar vid ett senare tillfälle.
2. Du vill starta runbooken, så klicka på **Start** och sedan på **OK** när bladet starta Runbook öppnas.
3. Ett jobb fönster öppnas för det Runbook-jobb som du har skapat. Du kan stänga det här fönstret, men i det här fallet lämnar du det öppet så att du kan se jobbets förlopp.
1. Jobbets status visas i **jobb Sammanfattning** och matchar de status värden som du såg när du testade runbooken.
2. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Fönstret utdata öppnas och du kan se din *Hello World*.
3. Stäng utdatafönstret.
4. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbook-jobbet skriver till dem.
5. Stäng fönstret strömmar och fönstret jobb för att återgå till fönstret MyFirstRunbook-python.
6. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.
7. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägg till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser.
För att hantera Azure-resurser måste skriptet autentisera med hjälp av autentiseringsuppgifterna från ditt Automation-konto. För att hjälpa dig kan du använda [Azure Automation Utility-paketet](https://github.com/azureautomation/azure_automation_utility) för att göra det enklare att autentisera och interagera med Azure-resurser.

> [!NOTE]
> Automation-kontot måste ha skapats med tjänstens huvud namns funktion för att det ska finnas ett Kör som-certifikat.
> Om ditt Automation-konto inte har skapats med tjänstens huvud namn kan du autentisera med hjälp av metoden som beskrivs i [autentisera med Azures hanterings bibliotek för python](/azure/python/python-sdk-azure-authenticate).

1. Öppna text redigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-python.

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

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Lägg till kod för att skapa python Compute Client och starta den virtuella datorn

Om du vill arbeta med virtuella Azure-datorer skapar du en instans av [Azure Compute-klienten för python](https://docs.microsoft.com/python/api/azure.mgmt.compute.computemanagementclient?view=azure-python).

Använd Compute-klienten för att starta den virtuella datorn. Lägg till följande kod i runbooken:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Där _MyResourceGroup_ är namnet på den resurs grupp som innehåller den virtuella datorn och _testvm_ är namnet på den virtuella dator som du vill starta.

Testa och kör runbooken igen för att se att den startar den virtuella datorn.

## <a name="use-input-parameters"></a>Använda indataparametrar

Runbooken använder för närvarande hårdkodade värden för namnen på resurs gruppen och den virtuella datorn.
Nu ska vi lägga till kod som hämtar dessa värden från indataparametrar.

Du kan använda `sys.argv` variabeln för att hämta parameter värden.
Lägg till följande kod i Runbook omedelbart efter de andra `import` -satserna:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Detta importerar `sys` modulen och skapar två variabler för att lagra resurs gruppen och namn på virtuella datorer.
Observera att elementet i argument listan `sys.argv[0]`, är namnet på skriptet och inte indata från användaren.

Nu kan du ändra de två sista raderna i runbooken så att de använder värdena för Indataparametern i stället för att använda hårdkodade värden:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

När du startar en python-Runbook (antingen på **test** sidan eller som en publicerad Runbook) kan du ange värden för parametrarna på sidan **starta Runbook** under **parametrar**.

När du börjar ange ett värde i den första rutan visas en sekund, och så att du kan ange så många parameter värden som behövs.

Värdena är tillgängliga för skriptet som `sys.argv` matrisen som i den kod som du nyss lade till.

Ange namnet på din resurs grupp som värde för den första parametern, och namnet på den virtuella dator som ska starta som värdet för den andra parametern.

![Ange parameter värden](media/automation-first-runbook-textual-python/runbook-python-params.png)

Starta runbooken genom att klicka på **OK** . Runbooken körs och startar den virtuella dator som du har angett.

## <a name="next-steps"></a>Nästa steg

- Se hur du kommer igång med PowerShell-runbooks i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
- Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
- Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md)
- Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
- Information om hur du utvecklar för Azure med python finns i [Azure för python-utvecklare](/azure/python/)
- Om du vill visa exempel på python 2-Runbooks går du till [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
