# Scopes
> You can use scopes in your codes. They are basically single-line comments, which are parsed by Hikka and t.me/hikkamods_bot

```# scope: hikka_only``` - *Redundant*: Modules, which are supported only on Hikka  
```# scope: inline``` - Modules, which use inline features. *This scope doesn't affect module directly*  
```# scope: hikka_min 1.1.1``` - Specify, which version of Hikka is required for your module to work properly  
```# scope: no_stats``` [1.2.10+] - Don't collect stats about your module loading (you can see stats in t.me/hikkamods_bot [/stats command] if you are a trusted developer)  
```# meta developer: @hikkamods``` - Specify the developer of module. If channel is specified, Hikka will suggest to subscribe and username will become clickable  
```# meta pic: https://img.icons8.com/cotton/344/moon-satellite.png``` - Specify link to module icon. Will be used in @hikkamods_bot  
```# meta banner: https://mods.hikariatama.ru/badges/spotify.jpg``` - Specify banner link. Will be used in @hikkamods_bot  
```# requires: Pillow requests spotipy``` - Specify requirements to install. If left unspecified, Hikka will try to determine requirements from error, but they're not always correct  


