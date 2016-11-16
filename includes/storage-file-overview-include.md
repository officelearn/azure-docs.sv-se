## <a name="overview"></a>Översikt
Azure File Storage är en tjänst som erbjuder filresurser i molnet med hjälp av [SMB-standardprotokollet (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Både SMB 2.1 och SMB 3.0 stöds. Med Azure File Storage kan du snabbt och utan kostsamma omskrivningar migrera äldre program som är beroende av filresurser till Azure. Program som körs på virtuella Azure-datorer eller molntjänster eller från lokala klienter kan montera en filresurs i molnet, precis som ett skrivbordsprogram monterar en typisk SMB-resurs. Ett obegränsat antal programkomponenter kan sedan montera och komma åt fillagringsresursen samtidigt.

Eftersom en fillagringsresurs är en vanlig SMB-filresurs kan program som körs i Azure komma åt data i resursen via filsystemets I/O-API:er. Utvecklare kan därför utnyttja befintlig kod och erfarenhet för att migrera befintliga program. IT-proffs kan använda PowerShell-cmdlets för att skapa, montera och hantera fillagringsresurser som en del av administrationen av Azure-program.

Du kan skapa Azure-filresurser med hjälp av [Azure Portal](https://portal.azure.com), PowerShell-cmdlets för Azure Storage, klientbiblioteken för Azure Storage eller Azure Storage REST-API:et. Och eftersom alla dessa filresurser är SMB-resurser kan du dessutom komma åt dem via vanliga och välbekanta filsystem-API:er.



<!--HONumber=Nov16_HO2-->


