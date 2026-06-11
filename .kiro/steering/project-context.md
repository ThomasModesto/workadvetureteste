# WorkAdventure Map Starter Kit — Contexto do Projeto

## O que é este projeto

Mapa personalizado para o [WorkAdventure](https://workadventu.re) — uma plataforma de escritório virtual com avatares 2D. O projeto usa o **Map Starter Kit** oficial da WorkAdventure com Vite + TypeScript.

## Stack

- **Vite** (bundler + dev server)
- **TypeScript**
- **WorkAdventure Scripting API** (`@workadventure/iframe-api-typings`, `@workadventure/scripting-api-extra`)
- **wa-map-optimizer-vite** (otimização de tilesets)
- **vite-plugin-node** (servidor Express embutido no Vite)
- **vite-plugin-mkcert** (HTTPS local para compatibilidade com `play.workadventu.re`)
- **Express** via `@workadventure/map-starter-kit-core`

## Estrutura de pastas

```
workAdventure/
├── app/
│   └── app.ts              # Entry point do servidor Express (não modificar)
├── src/
│   └── main.ts             # Scripts do mapa (executados no browser pelo WorkAdventure)
├── tilesets/               # Todos os PNGs de tilesets usados no mapa
│   ├── gather_v1/          # Tilesets do Gather (floors, walls, terrains)
│   ├── moderninteriors-win/# Modern Interiors (16x16, 32x32, 48x48)
│   ├── modernexteriors-win/# Modern Exteriors
│   ├── kenney_roguelike-modern-city/
│   ├── kenney_rpg-urban-pack/
│   ├── Modern_Interiors_Free_v2.2/
│   ├── WA_Assets/          # Assets oficiais WorkAdventure
│   └── ...
├── office.tmj              # Ficheiro do mapa principal (formato Tiled JSON)
├── web.vite.config.ts      # Config Vite para desenvolvimento
├── buildmap.vite.config.ts # Config Vite para build de produção
├── .env                    # Variáveis de ambiente (UPLOAD_MODE, LOG_LEVEL, etc.)
├── package.json
└── tsconfig.json
```

## Comandos

| Comando | O que faz |
|---|---|
| `npm run dev` | Inicia o servidor de desenvolvimento em `https://localhost:5173` |
| `npm run buildmap` | Build de produção do mapa |
| `npm run upload` | Build + upload para WA Map Storage |

## Como testar o mapa

1. Correr `npm run dev`
2. Aceitar o certificado auto-assinado no browser (`https://localhost:5173`)
3. Aceder ao WorkAdventure em `https://play.workadventu.re/_/global/localhost:5173/office.tmj`

## HTTPS obrigatório

O `play.workadventu.re` é HTTPS e não consegue carregar recursos de `http://localhost`. Por isso o servidor local **deve correr em HTTPS**. Está configurado com `vite-plugin-mkcert` + `https: true` no `web.vite.config.ts`.

## Ficheiro do mapa — office.tmj

- Formato: Tiled Map JSON (`.tmj`)
- Todos os caminhos de tilesets são **relativos ao ficheiro do mapa** (ex: `tilesets/gather_v1/gather_floors_4.1.png`)
- Caminhos que apontem para fora do projeto (`../`) causam `NETWORK_ERROR` no WorkAdventure
- Scripts do mapa são referenciados pela propriedade `"script": "src/main.ts"`

## Scripts do mapa (src/)

- Todos os scripts TypeScript do mapa **devem estar em `src/`**
- São compilados automaticamente pelo Vite e servidos com MIME type correto
- O `main.ts` atual tem um exemplo de popup ao entrar na área `clock`

## Variáveis de ambiente (.env)

```
LOG_LEVEL=1                        # 0=none, 1=info, 2=verbose
TILESET_OPTIMIZATION=false         # true para comprimir tilesets no build
UPLOAD_MODE=MAP_STORAGE            # ou GH_PAGES
```

## Tilesets presentes no projeto

Todos os tilesets foram copiados para dentro de `tilesets/` para evitar dependências de caminhos externos:

- `gather_v1/gather_floors_4.1.png`
- `gather_v1/gather_walls_3.1.png`
- `gather_v1/gather_terrains_4.0.png`
- `moderninteriors-win/1_Interiors/{16x16,32x32,48x48}/`
- `modernexteriors-win/Modern_Exteriors_32x32/Modern_Exteriors_Complete_Tileset_32x32.png`
- `kenney_roguelike-modern-city/Preview.png`
- `kenney_rpg-urban-pack/Sample.png` + `Tilemap/tilemap.png`
- `Modern_Interiors_Free_v2.2/Modern tiles_Free/free_overview.png`
- Assets WA oficiais (`WA_Assets/`, `WA_Room_Builder.png`, etc.)
- Modern Interiors (`moderninteriors/1_Interiors/`)

## Problemas conhecidos e resoluções

### NETWORK_ERROR ao carregar o mapa
**Causa:** Caminhos de tilesets no `.tmj` apontam para fora do projeto (`../`).  
**Solução:** Copiar o ficheiro para `tilesets/` e corrigir o caminho no `.tmj`.

### ERR_SSL_PROTOCOL_ERROR
**Causa:** O servidor local corre em HTTP mas `play.workadventu.re` exige HTTPS.  
**Solução:** `vite-plugin-mkcert` + `https: true` no `web.vite.config.ts`.

### TypeError: Cannot read properties of undefined (reading 'readable')
**Causa:** `@vitejs/plugin-basic-ssl` é incompatível com `vite-plugin-node`.  
**Solução:** Usar `vite-plugin-mkcert` em vez de `basicSsl`.
