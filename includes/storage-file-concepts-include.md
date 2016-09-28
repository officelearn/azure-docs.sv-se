## Vad är Azure File Storage?

File Storage erbjuder delad lagring för program som använder standardprotokollen SMB 2.1 eller SMB 3.0. Virtuella Microsoft Azure-datorer och Azure-molntjänster kan dela fildata över programkomponenter via monterade resurser, och lokala program kan ha åtkomst till fildata i en resurs via File Storage-API:n.

Program som körs på virtuella Azure-datorer eller Azure-molntjänster kan montera en File Storage-resurs för åtkomst till fildata, på samma sätt som ett skrivbordsprogram monterar en vanlig SMB-resurs. Ett obegränsat antal virtuella Azure-datorer eller roller kan montera och ha åtkomst till File Storage-resursen samtidigt.

Eftersom en File Storage-resurs är en standard filresurs i Azure och använder sig av SMB-protokollet, får program som körs i Azure åtkomst till data i resursen via API:er för fil-I/O. Utvecklare kan därför utnyttja befintlig kod och erfarenhet för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdlets för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program. Den här guiden visar exempel på båda.

Vanliga användningsområden för File Storage är:

- Migrering av lokala program som är beroende av filresurser för att köras på virtuella Azure-datorer eller Azure-molntjänster utan dyra omskrivningar
- Lagring av delade programinställningar, exempelvis konfigurationsfiler
- Lagring av diagnostiska data, exempelvis loggar, mått och kraschdumpar på en delad plats 
- Lagring av verktyg och hjälpmedel som behövs för att utveckla och administrera virtuella Azure-datorer eller Azure-molntjänster

## File Storage-koncept

File Storage består av följande komponenter:

![filkoncept][files-concepts]

-   **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Se [Skalbarhets- och prestandamål för Azure Storage](../articles/storage/storage-scalability-targets.md) för information om kapacitet för lagringskonton.

-   **Resurs:** en File Storage-resurs är en SMB-filresurs i Azure. 
    Alla kataloger och filer måste skapas i en överordnad resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till 5 TB total kapacitet för filresursen.

-   **Katalog:** en valfri hierarki med kataloger. 

-   **Fil:** en fil i resursen. En fil kan vara upp till 1 TB stor.

-   **URL-format:** filer är adresserbara via följande URL-format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    Följande exempel-URL skulle kunna användas för att adressera en av filerna i ovanstående diagram:  
    `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

Mer information om hur du namnger resurser, kataloger och filer finns i [namnge och referera till resurser, kataloger, filer och metadata](http://msdn.microsoft.com/library/azure/dn167011.aspx).

[filkoncept]: ./media/storage-file-concepts-include/files-concepts.png

<!--HONumber=Sep16_HO3-->


