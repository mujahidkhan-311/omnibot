const { default: makeWASocket, useMultiFileAuthState, fetchLatestBaileysVersion } = require("@whiskeysockets/baileys");

async function startBot() {
    const { state, saveCreds } = await useMultiFileAuthState("auth_info");
    const { version } = await fetchLatestBaileysVersion();
    const sock = makeWASocket({ version, auth: state });

    sock.ev.on("creds.update", saveCreds);

    sock.ev.on("messages.upsert", async ({ messages }) => {
        const msg = messages[0];
        if (!msg.message) return;
        const text = msg.message.conversation || msg.message.extendedTextMessage?.text;
        if (text && text.toLowerCase() === "hi") {
            await sock.sendMessage(msg.key.remoteJid, { text: "Hello! I am your WhatsApp bot." });
        }
    });

    console.log("Bot started. Scan the QR code to connect.");
}

startBot();
