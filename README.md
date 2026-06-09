# Banito Board

Página estática para registrar, organizar e visualizar screenshots de jogadores que entraram em cooldown durante partidas por comportamento duvidoso, como fogo amigo, trollagem ou decisões táticas indefensáveis.

O projeto é uma brincadeira entre amigos, com visual profissional e tom de arquivo de evidências.

## Estrutura do projeto

```text
banito/
  index.html
  data/
    screenshots.json
  imgs/
    screenshot-001.png
    screenshot-002.jpg
    screenshot-003.webp
```

## Como funciona

A página `index.html` carrega os dados do arquivo:

```text
data/screenshots.json
```

Cada registro do JSON aponta para uma imagem dentro da pasta `imgs`.

Exemplo:

```json
[
  {
    "imageUrl": "./imgs/example.png",
    "steamProfileUrl": "https://steamcommunity.com/profiles/76561198000000001",
    "playerName": "Mock Player 1",
    "timestamp": "2026-06-09T12:34:56.0000000Z",
    "screenshotBy": "Mock Screenshot Owner"
  }
]
```

## Campos do JSON

| Campo             | Descrição                               |
| ----------------- | --------------------------------------- |
| `imageUrl`        | Caminho da imagem dentro do repositório |
| `steamProfileUrl` | URL do perfil Steam do jogador          |
| `playerName`      | Nome do jogador exibido na página       |
| `timestamp`       | Data e hora da evidência em formato ISO |
| `screenshotBy`    | Pessoa responsável pelo screenshot      |

## Como adicionar uma nova evidência

1. Coloque a imagem na pasta `imgs`.
2. Abra o arquivo `data/screenshots.json`.
3. Adicione um novo objeto no array.
4. Preencha o caminho da imagem em `imageUrl`.
5. Faça commit e push para o GitHub.

Exemplo:

```json
{
  "imageUrl": "./imgs/player-ban-2026-06-09.png",
  "steamProfileUrl": "https://steamcommunity.com/id/mock-player",
  "playerName": "Mock Player",
  "timestamp": "2026-06-09T22:10:00.0000000Z",
  "screenshotBy": "Glauber Cruz"
}
```

## Gerar o JSON automaticamente pelo GitHub remoto

No PowerShell, rode:

```powershell
$repo = "ffginmp/banito"
$branch = "main"
$folder = "imgs"

$url = "https://api.github.com/repos/$repo/contents/$folder" + "?ref=$branch"

$files = Invoke-RestMethod -Uri $url -Headers @{
  "Accept" = "application/vnd.github+json"
  "User-Agent" = "PowerShell"
}

$index = 1

$details = $files |
  Where-Object {
    $_.type -eq "file" -and $_.name -match "\.(png|jpg|jpeg|webp|gif)$"
  } |
  Sort-Object name |
  ForEach-Object {
    $item = [PSCustomObject]@{
      imageUrl = "./imgs/$($_.name)"
      steamProfileUrl = "https://steamcommunity.com/profiles/7656119800000000$index"
      playerName = "Mock Player $index"
      timestamp = (Get-Date).ToUniversalTime().ToString("o")
      screenshotBy = "Mock Screenshot Owner"
    }

    $index++
    $item
  }

$details | ConvertTo-Json -Depth 10 | Set-Content -Path ".\data\screenshots.json" -Encoding UTF8

Get-Content ".\data\screenshots.json"
```

## Publicação no GitHub Pages

Este projeto foi pensado para funcionar como uma página estática no GitHub Pages.

Configuração sugerida:

1. Acesse o repositório no GitHub.
2. Vá em **Settings**.
3. Acesse **Pages**.
4. Em **Build and deployment**, selecione:

   * Source: `Deploy from a branch`
   * Branch: `main`
   * Folder: `/root`
5. Salve a configuração.

Depois disso, o GitHub Pages publicará o `index.html`.

## Observações

* O caminho das imagens deve continuar relativo ao `index.html`.
* Mesmo que o JSON esteja em `data/screenshots.json`, os valores de `imageUrl` devem apontar para `./imgs/...`.
* Não use links do tipo `/blob/main/`, pois eles abrem a página do GitHub, não a imagem diretamente.
* Prefira imagens `.webp`, `.jpg` ou `.png` otimizadas para evitar lentidão com o crescimento do arquivo histórico.

## Exemplo de caminho correto

```json
"imageUrl": "./imgs/screenshot-001.png"
```

## Exemplo de caminho incorreto

```json
"imageUrl": "https://github.com/ffginmp/banito/blob/main/imgs/screenshot-001.png"
```

## Licença moral

Este arquivo não prova competência, apenas documenta o momento exato em que ela deixou de existir temporariamente.
