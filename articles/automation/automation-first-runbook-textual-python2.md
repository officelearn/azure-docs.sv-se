---
title: Min första Python-runbook i Azure Automation
description: Självstudiekurs som hjälper dig att skapa, testa och publicera en enkel Python-runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b17a0403a3b2a3ff8c3586ed26a4b833db54922d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75365945"
---
# <a name="my-first-python-runbook"></a>Min första Python-runbook

> [!div class="op_single_selector"]
> - [Grafisk](automation-first-runbook-graphical.md)
> - [Powershell](automation-first-runbook-textual-powershell.md)
> - [PowerShell-arbetsflöde](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

Den här självstudien hjälper dig att skapa en [Python-runbook](automation-runbook-types.md#python-runbooks) i Azure Automation. Du börjar med en enkel runbook som du testar och publicerar. Sedan ändrar du runbook-jobbet så att det hanterar Azure-resurser. I det här exemplet ska det starta en virtuell dator i Azure. Slutligen gör du runbooken mer robust genom att lägga till runbook-parametrar.

> [!NOTE]
> Det går inte att använda en webhook för att starta en Python-runbook.

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

- En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ett [Automation-konto för Azure](automation-offering-get-started.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser. Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
- En virtuell dator i Azure. Eftersom du ska stoppa och starta den här datorn bör det inte vara en virtuell dator som finns i produktionsmiljön.

## <a name="create-a-new-runbook"></a>Skapa en ny runbook

Du börjar med att skapa en enkel runbook som matar ut texten *Hello World*.

1. Öppna ditt Automation-konto på Azure Portal.

    Automation-kontosidan innehåller en snabb översikt över resurserna i det här kontot. Du bör redan ha vissa tillgångar. De flesta av dessa tillgångar är de moduler som ingår automatiskt i ett nytt Automation-konto. Du behöver även autentiseringstillgången som nämns i [kravavsnittet](#prerequisites).<br>

1. Välj **Runbooks** under **PROCESSHANTERING** för att öppna listan med runbook-jobb.
1. Välj **+ Lägg till en runbook** om du vill skapa en ny runbook.
1. Ge runbooken namnet *MyFirstRunbook-Python*.
1. I det här fallet ska du skapa en [Python-runbook](automation-runbook-types.md#python-runbooks) så välj **Python 2** för **Runbook typ**.
1. Klicka på **Skapa** för att skapa runbooken och öppna textredigeraren.

## <a name="add-code-to-the-runbook"></a>Lägga till kod i runbooken

Nu kan du lägga till ett enkelt kommando för att skriva ut texten "Hello World":

```python
print("Hello World!")
```

Klicka på **Spara** om du vill spara runbooken.

## <a name="test-the-runbook"></a>Testa runbooken

Innan du publicerar runbook-jobbet så att den blir tillgänglig i produktionsmiljön testar du den och kontrollerar att den fungerar som den ska. När du testar en runbook kör du dess **utkastversion** och visar dess utdata interaktivt.

1. Öppna testfönstret genom att klicka på **Testfönster**.
1. Starta testet genom att klicka på **Starta**. Detta bör vara det enda aktiverade alternativet.
1. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas.
   Jobbets första status är *I kö*, vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig. Den flyttas till *Start* när en arbetare gör anspråk på jobbet och sedan *körs* när runbooken faktiskt börjar köras.
1. När runbook-jobbet är klart visas dess utdata. I det här fallet bör du se *Hello World*.
1. Gå tillbaka till arbetsytan genom att stänga testfönstret.

## <a name="publish-and-start-the-runbook"></a>Publicera och starta runbooken

Runbook som du skapade är fortfarande i utkastläge. Du måste publicera den innan du kan köra den i produktion.
När du publicerar en runbook skriver du över den befintliga publicerade versionen med utkastversionen.
I det här fallet har du inte en publicerad version ännu eftersom du just har skapat runbooken.

1. Klicka på **Publicera** för att publicera runbooken och sedan på **Ja** när du uppmanas att göra det.
1. Om du bläddrar åt vänster för att visa runbooken i rutan **Runbooks** nu visas en **redigeringsstatus för** **publicerad**.
1. Bläddra tillbaka till höger för att visa rutan för **MyFirstRunbook-Python**.
   Alternativen överst gör det möjligt för oss att starta runbooken, visa runbooken eller schemalägga den så att den startar någon gång i framtiden.
2. Du vill starta runbooken, så klicka på **Start** och klicka sedan på **Ok** när bladet Start Runbook öppnas.
3. Ett jobbfönster öppnas för det runbook-jobb som du skapade. Du kan stänga den här rutan, men i det här fallet lämnar du den öppen så att du kan titta på jobbets förlopp.
1. Jobbstatusen visas i **Jobbsammanfattning** och matchar de statusar som du såg när du testade runbooken.
2. När runbookens status visas som *Slutförd* klickar du på **Utdata**. Utdatafönstret öppnas och du kan se din *Hello World*.
3. Stäng utdatafönstret.
4. Klicka på **Alla loggar** för att öppna fönstret Strömmar för runbook-jobbet. Du bör endast se *Hello World* i utdataströmmen, men även andra dataströmmar kan visas för ett runbook-jobb, till exempel Utförlig och Fel, om runbook-jobbet skriver till dem.
5. Stäng fönstret Strömmar och jobbfönstret för att återgå till fönstret MyFirstRunbook-Python.
6. Öppna fönstret Jobb för runbooken genom att klicka på **Jobb**. Här visas alla jobb som skapats av den här runbooken. Nu bör du endast se ett jobb eftersom du bara körde jobbet en gång.
7. Du kan klicka på det här jobbet för att öppna samma jobbfönster som du visade när du startade runbook-jobbet. På så sätt kan du gå tillbaka i tiden och visa information om alla jobb som har skapats för en specifik runbook.

## <a name="add-authentication-to-manage-azure-resources"></a>Lägga till autentisering för att hantera Azure-resurser

Du har testat och publicerat din runbook, men hittills gör den egentligen inget användbart. Du vill att den ska hantera Azure-resurser.
För att hantera Azure-resurser måste skriptet autentisera med hjälp av autentiseringsuppgifterna från ditt Automation-konto. För att hjälpa dig kan du använda [Azure Automation-verktygspaketet](https://github.com/azureautomation/azure_automation_utility) för att göra det enklare att autentisera och interagera med Azure-resurser.

> [!NOTE]
> Automation-kontot måste ha skapats med tjänstens huvudfunktion för att det ska finnas ett Run As-certifikat.
> Om ditt automatiseringskonto inte skapades med tjänstens huvudnamn kan du autentisera med den metod som beskrivs på [Authenticate med Azure Management Libraries for Python](/azure/python/python-sdk-azure-authenticate).

1. Öppna textredigeraren genom att klicka på **Redigera** i fönstret MyFirstRunbook-Python.

2. Lägg till följande kod för att autentisera i Azure:

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

Om du vill arbeta med virtuella Azure-datorer skapar du en instans av [Azure Compute-klienten för Python](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Använd beräkningsklienten för att starta den virtuella datorn. Lägg till följande kod i runbook:

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

Där _MyResourceGroup_ är namnet på resursgruppen som innehåller den virtuella datorn och _TestVM_ är namnet på den virtuella dator som du vill starta.

Testa och kör runbook igen för att se att den startar den virtuella datorn.

## <a name="use-input-parameters"></a>Använd indataparametrar

Runbook använder för närvarande hårdkodade värden för namnen på resursgruppen och den virtuella datorn.
Nu ska vi lägga till kod som hämtar dessa värden från indataparametrar.

Du använder `sys.argv` variabeln för att hämta parametervärdena.
Lägg till följande kod i runbooken direkt efter de andra `import` uttalandena:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Detta importerar modulen `sys` och skapar två variabler för att hålla resursgruppen och VM-namnen.
Observera att elementet i `sys.argv[0]`argumentlistan , är namnet på skriptet och inte matas in av användaren.

Nu kan du ändra de två sista raderna i runbooken så att de använder indataparametervärdena i stället för att använda hårdkodade värden:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

När du startar en Python-runbook (antingen på **sidan Test** eller som en publicerad runbook) kan du ange värdena för parametrar på sidan **Startkörning** under **Parametrar**.

När du har börjat ange ett värde i den första rutan visas en sekund och så vidare, så att du kan ange så många parametervärden som behövs.

Värdena är tillgängliga för `sys.argv` skriptet som matrisen som i koden du just lagt till.

Ange namnet på resursgruppen som värde för den första parametern och namnet på den virtuella datorn som ska börja som värdet för den andra parametern.

![Ange parametervärden](media/automation-first-runbook-textual-python/runbook-python-params.png)

Klicka på **OK** för att starta runbooken. Runbooken körs och startar den virtuella datorn som du angav.

## <a name="error-handling-in-python"></a>Felhantering i Python

Du kan också använda följande konventioner för att hämta olika strömmar från python-runbooks, inklusive **VARNING,** **FEL**och **DEBUG-strömmar.**

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

I följande exempel visas den `try...except` här konventionen som används i ett block.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> **sys.stderr** stöds inte i Azure Automation.

## <a name="next-steps"></a>Nästa steg

- Mer om du vill komma igång med PowerShell-runbooks, se [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md)
- Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
- Information om hur du kommer igång med PowerShell-arbetsflödeskörningsböcker läser du [Min första PowerShell-arbetsflödeskörningsbok](automation-first-runbook-textual.md)
- Mer information om typer av runbooks, och om deras fördelar och begränsningar, finns i [Typer av Azure Automation-runbooks](automation-runbook-types.md)
- Mer information om hur du utvecklar för Azure med Python finns i [Azure för Python-utvecklare](/azure/python/)
- Information om hur du visar exempel på Python 2-runbooks finns i [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)
