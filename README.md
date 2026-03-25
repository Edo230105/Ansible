# ansible-visual-basic

Role Ansible per Windows che sostituisce lo script **VBScript** del flusso **IOFIN Antiterrorismo** (SimCorp Sofia): controlla la share Sofia, copia il CSV in input locale, lancia l’ETL, verifica l’output, pulisce la share e scrive un log.

## Cosa fa (flusso)
1. Verifica che la cartella su Share 1 esista.
2. Controlla la presenza del file semaforo `Antiter_Fine.csv`.
3. Copia `Enti_4_Antiterrorismo.csv` in `C:\Scarico\IOFIN\ETL\Input\Antiterr\`.
4. Esegue `ETLFileManager.Cliente.exe -AN:antiterr` e attende la fine.
5. Verifica che esista `C:\Scarico\IOFIN\ETL\Output\Antiterr\Antiter.txt`.
6. Se OK, cancella dalla share `Antiter_Fine.csv` e `Enti_4_Antiterrorismo.csv`.
7. Scrive una riga di log in `C:\ITFIN_LOG\ITF_IOFIN_ANTITERRORISMO.Log`.
8. Se `Ret != 0` il role fallisce.

## Codici Ret
- `0`  = OK
- `10` = file semaforo assente
- `20` = output mancante (export fallito)
- `30` = share non raggiungibile
- `50` = cartella su share non presente
- `60-61-62` = File su local NON copiato CORRETTAMENTE
- `70-71-72` = Folder Input su local non presente
- `80` = Folder Output su local non presente
- `90` = Folder Log su local non presente
- `100` = File su Folder Input local non presente
- `110` = File su Folder SAV local non presente

## Requisiti
- Host target: **Windows**
- Accesso alla share `\\direzione.gr-u.it\data\ITFIN_SOFIA` con le credenziali usate per eseguire Ansible (o via `become_method: runas`)
- Eseguibile ETL presente su target: `C:\Scarico\IOFIN\Binary\ETLFileManager.Cliente.exe`

## Variabili (defaults)
File: `defaults/main.yml`

Principali:
- `sofia_share_root`, `sofia_subfolder`
- `path_in`, `path_out`, `path_prog`
- `file_check`, `file_in`, `file_out`
- `path_log`, `file_log`
- `etl_exe`, `etl_arg`

## Esempio di utilizzo
```yaml
- name: Run Iofin Antiterrorismo ETL
  hosts: windows_etl_host
  gather_facts: true
  roles:
    - ansible-visual-basic
