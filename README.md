# PLAY COMMAND (EXPERIMENTAL) :

```
Secktor.cmd({
    pattern: "ytdl",
    desc: "Searches YouTube and downloads the first song.",
    category: "media",
    react: "🎵",
    filename: __filename
},
async (Void, citel, text) => {
    if (!text) return await citel.reply("*❌ Please provide a search term.*");
    
    const searchUrl = `https://api.giftedtech.my.id/api/search/yts?apikey=gifted&query=${encodeURIComponent(text)}`;
    
    try {
        let searchResponse = await fetch(searchUrl);
        let searchData = await searchResponse.json();

        if (!searchData.success || !searchData.results.length) {
            return await citel.reply("*❌ No results found for the given query.*");
        }

        const firstResult = searchData.results[0];
        const videoTitle = firstResult.title;
        const videoUrl = firstResult.url;

        const downloadUrl = `https://api.giftedtech.my.id/api/download/ytmp3?apikey=gifted&url=${encodeURIComponent(videoUrl)}`;
        let downloadResponse = await fetch(downloadUrl);
        let downloadData = await downloadResponse.json();

        if (!downloadData.success || !downloadData.result.download_url) {
            return await citel.reply("*❌ Failed to download the song.*");
        }

        const downloadLink = downloadData.result.download_url;

        await Void.sendMessage(citel.chat, { 
            audio: { url: downloadLink }, 
            mimetype: 'audio/mpeg', 
            ptt: false 
        }, { quoted: citel });

        await citel.reply(`*🎶 Downloaded Song:* ${videoTitle}\n*🔗 Source:* ${videoUrl}`);
    } catch (error) {
        console.error(error);
        return await citel.reply("*❌ An error occurred while processing your request.*");
    }
});
```
