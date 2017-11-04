## <a name="transfer-local-files-to-cloud-shell"></a>Överföring av lokala filer till molnet Shell
Den `clouddrive` directory synkroniseras med Azure portal lagring-bladet. Använd det här bladet för överföring av lokala filer till eller från filresursen. Uppdatera filer i molnet Shell avspeglas i fillagring GUI när du uppdaterar bladet.

### <a name="download-files"></a>Hämta filer

![Lista över lokala filer](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Gå till den monterade filresursen i Azure-portalen.
2. Välj målfilen.
3. Välj den **hämta** knappen.

### <a name="upload-files"></a>Överföra filer

![Lokala filer som ska överföras](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Gå till din monterade filresurs.
2. Välj den **överför** knappen.
3. Välj den eller de filer som du vill överföra.
4. Bekräfta överföringen.

Du bör nu se de filer som är tillgängliga i din `clouddrive` katalog i molnet Shell.