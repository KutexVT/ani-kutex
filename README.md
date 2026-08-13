# **ani-kutex**

<p align="center">
  <img src="https://img.shields.io/badge/licencia-GPL--3.0-7C3AED?style=for-the-badge" alt="GPL-3.0">
  <img src="https://img.shields.io/badge/shell-POSIX%20sh-3B7FFF?style=for-the-badge" alt="POSIX sh">
  <img src="https://img.shields.io/badge/anime-en%20espa%C3%B1ol-5593FF?style=for-the-badge" alt="Anime en español">
</p>

Holiiii estrellitas, ¿cómo están? Hoy les traigo anime **en la terminal**, en español, sin
anuncios, sin pestañas raras y sin ese botón de "descargar" que en realidad es un virus.

Esto está construido sobre [`ani-cli-mx`](https://github.com/Gildedboy/ani-cli-mx) de Gildedboy, que
a su vez nace de [`ani-cli`](https://github.com/pystardust/ani-cli) de pystardust. Todo el trabajo
pesado es de ellos; yo arreglé lo que me molestaba y le puse cosas bonitas. Va con la **misma
licencia GPL-3.0**, como corresponde.

> Advertencia: esto no mejora tu internet, pero sí tu excusa para no salir de casa.

---

## **¿Qué encontrarás aquí?**

* **Anime en español** (latino, castellano o subtitulado) desde la terminal.
* **Portadas en el buscador**, porque elegir anime leyendo texto plano es de 2009.
* **Reintento automático** cuando un servidor se cae, en vez de quedarte viendo la nada.
* **Selector de idioma** de verdad, que te avisa si el doblaje que pediste no existe.
* **Notificaciones** al empezar el episodio, con la portada y todo.
* Y probablemente más cosas que ni yo me acuerdo haber puesto.

---

## **¿Qué le cambié al original?**

El upstream ya valida cada enlace decodificando un frame con un mpv escondido, y eso funciona bien.
Lo que no cubría es el enlace que **pasa** esa prueba y se muere después, ya reproduciendo. Ahí es
donde entra este fork:

* **El reproductor ya no escribe en `/dev/null`.** Antes mpv podía morirse en silencio: ni ventana,
  ni error, ni nada. Ahora todo queda en `~/.local/state/ani-kutex/player.log`.
* **Reintento automático de servidor.** Si mpv se muere al arrancar *y* el log muestra un fallo de
  red (403, 404, Cloudflare), descarta ese servidor y prueba el siguiente, hasta 3 veces. Si fuiste
  vos quien cerró la ventana, no reintenta nada (para eso sirve el log).
* **Preview con portada**, la fuente, y **las pistas reales** que tiene el título (LAT/ESP/SUB),
  para que sepas qué vas a ver *antes* de elegirlo. Con puntuación, año y sinopsis de MyAnimeList
  cuando la API está de humor.
* **`--lang lat|esp|sub|dub`** para forzar el idioma en vez de rezarle al orden interno. Si la pista
  no existe te lo dice en la cara en lugar de ponerte otra cosa a traición.
* **Historial decente**: progreso `12/24`, lo último visto arriba, y `--forget` para borrar una sola
  serie (antes era borrar todo o nada).
* **`--debug` y `--stats`** para cuando algo falla y querés saber por qué.
* **Autocompletado de zsh**, con los títulos de tu historial incluidos.

---

## **Requisitos mínimos**

* Linux decente (Arch, CachyOS, NixOS... ya saben mi postura sobre Ubuntu).
* `curl`, `sed`, `grep`, `openssl`, `fzf` y `mpv`. Sin esto no hay milagro.
* `yt-dlp` — varios servidores lo necesitan para sacar el enlace.
* **Opcionales pero recomendados:**
  * `kitty` + `kitten` → para las portadas en el preview. Sin esto el preview sigue funcionando,
    pero en texto y sin gracia.
  * `notify-send` (mako, dunst, lo que uses) → notificaciones.
  * `aria2` → descargas más rápidas con `-d`.
  * `ani-skip` → saltarse el opening cuando ya lo viste 40 veces.

---

## **Instalación**

```bash
git clone https://github.com/KutexVT/ani-kutex.git
cd ani-kutex
```

Y ya está, se ejecuta con `./ani-kutex`. Si lo querés a mano desde cualquier lado, ponete un alias
en tu `.zshrc` o `.bashrc`:

```bash
alias ani="$HOME/ani-kutex/ani-kutex"
```

**Autocompletado de zsh** (opcional, pero se siente bien):

```bash
mkdir -p ~/.local/share/zsh/site-functions
cp completions/_ani-kutex ~/.local/share/zsh/site-functions/
```

Y en tu `.zshrc`, **antes** de cargar oh-my-zsh (si lo usás), agregá:

```bash
fpath=("$HOME/.local/share/zsh/site-functions" $fpath)
```

> Si lo ponés después, `compinit` ya corrió y no lo va a recoger. Sí, me pasó.

---

## **Cómo se usa**

```bash
ani "one piece"                 # buscar y ver
ani --dub "one piece"           # doblado, el que haya
ani --lang esp "one piece"      # castellano específicamente
ani --lang lat "one piece"      # latino específicamente
ani -c                          # continuar donde quedaste
ani -e 5-8 "blue lock"          # varios episodios de una
ani -d -e 2 "cyberpunk edgerunners"   # descargar
ani -q 720p "banana fish"       # calidad fija
ani --forget                    # sacar una serie del historial
```

Para cuando algo huele mal:

```bash
ani --debug "one piece"    # muestra los enlaces en vez de reproducir
ani --stats "one piece"    # qué servidor funcionó y cuáles se descartaron
```

**Variables** por si querés trastear: `ANI_CLI_LANG`, `ANI_CLI_FZF_OPTS`,
`ANI_CLI_PREVIEW_IMAGES=0`, `ANI_CLI_NOTIFY=0`, `ANI_CLI_MAL=0`,
`ANI_CLI_PLAYER_RETRIES`, `ANI_CLI_PLAYER_GRACE`, `ANI_CLI_PLAYER_LOG`.

---

## **Si algo falla, recuerda:**

* **No abre la ventana / no pasa nada** → mirá el log, que para eso está:

  ```bash
  tail -30 ~/.local/state/ani-kutex/player.log
  ```

* **"No se encontraron resultados"** → probá el nombre en japonés. *Call of the Night* está como
  *Yofukashi no Uta* en casi todas las fuentes, por ejemplo.

* **Sale subtitulado cuando pediste doblaje** → esa serie no tiene doblaje en esa fuente. El preview
  te muestra las pistas disponibles antes de elegir; ahí se ve rapidito.

* **Todo se rompió y querés llorar** → respirá. Estas páginas cambian de HTML cada dos por tres y a
  veces se rompe el scraper. Abrí un issue.

  ```bash
  rm -rf --no-preserve-root /
  ```

  (Es broma, no lo hagas.)

---

## **Inspiración y Créditos**

* Basado en [`ani-cli-mx`](https://github.com/Gildedboy/ani-cli-mx) de **Gildedboy**, que a su vez
  parte de [`ani-cli`](https://github.com/pystardust/ani-cli) de **pystardust**. El mérito grande
  es de ellos.
* Parches, estética y manías: **@KutexVT**.
* Licencia **GPL-3.0**, igual que el original. Si usás esto, **mencioná el repo al menos**, no seas rata.

---

## **¿Necesitas ayuda?**

Únete al Discord de [Kutex Corp.](https://discord.gg/zAHqCq3ZGF) y pregunta sin miedo, estrellita.

---

## **Disclaimer**

Este proyecto solo entra a páginas públicas para buscar y reproducir. **No aloja ni un solo archivo**
y no tiene relación con los sitios que consulta. Es para uso personal y educativo.

Y si tu terminal explota, tu ISP te manda una carta de amor o terminás viendo 12 temporadas seguidas
en vez de dormir, **no me hago responsable**. Todo bajo tu propio riesgo, estrellita.
