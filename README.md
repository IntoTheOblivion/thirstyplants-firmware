# ThirstyPlants · firmware

Qui stanno **solo i binari** dei firmware, serviti da
`raw.githubusercontent.com` perché la catena TLS di GitHub è Let's Encrypt, la
stessa radice che sonda e centralina hanno già in flash. Il sorgente vive altrove,
in un repository privato.

## Perché un repository pubblico

Le schede si aggiornano da sole: l'app legge un catalogo su Supabase, l'utente
sceglie se installare, e la scheda scarica il `.bin` in HTTPS **verificando il
certificato** contro ISRG Root X1 / YR. `github.com` è firmato Sectigo e non
funziona; `raw.githubusercontent.com` è firmato Let's Encrypt e funziona.

Il binario non contiene segreti: password del Wi-Fi, utente e password del broker
stanno nella memoria della scheda e non finiscono mai nel `.bin`.

## Come si pubblica

Dal repository privato, con lo script `tool/pubblica_firmware.ps1`:

```powershell
powershell -File tool/pubblica_firmware.ps1 -Tipo sonda -Versione 2.4.0
powershell -File tool/pubblica_firmware.ps1 -Tipo centralina -Versione 1.12.0
```

Lo script compila, verifica che la versione combaci con `FIRMWARE_VERSION`,
carica il file qui come `<sonda|centralina>/aquasmart_<higrow|trelay>_<versione>.bin`
e stampa la riga SQL da incollare nel catalogo di Supabase. **I nomi portano la
versione e non si sovrascrivono mai**: il catalogo punta all'impronta SHA-256 di
quel file, e un file sostituito renderebbe le vecchie righe bugiarde.

## I file

- `sonda/` — LilyGO T-HiGrow (T-HiGrow V1.1). La 2.4.0 è la prima che sa
  aggiornarsi da sola: va caricata **una volta via cavo**, con partizioni
  `min_spiffs`.
- `centralina/` — ESP32-S3 T-Relay. L'aggiornamento a distanza c'è dalla 1.11.0.
