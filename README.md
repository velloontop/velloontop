const developer_Name = "Ardit , ALAE_444#9999"; // My Account Name
const developer_Id = "1049774875134394388"; // My Account ID.
const YouTube_Channel = "ALAE_444" // YouTube Channel.

// اتمنا منك تتابعني على اليوتيوب
// -------------------
// I hope to your sub on my YouTube channel


const express = require('express');
const app = express();

const { Client, MessageEmbed } = require("discord.js");
const client = new Client({
  intents: 32767
});
const db = require('quick.db');

const admin = "1049774875134394388"; // ايدي الي يتحكم بالبوت
const prefix = "*"; // بادئة الاوامر

if (process.env.token) {
client.login(process.env.token);
} 
if (process.env.TOKEN){
  client.login(process.env.TOKEN);
}

client.on('ready', () => {
  client.user.setStatus("online");
  client.user.setActivity(`Bot Broadcast`, {
    type: "PLAYING"
  });
  console.log(`Logged in as ${client.user.tag} | https://discord.com/api/oauth2/authorize?client_id=${client.user.id}&permissions=0&scope=bot`);
});



// Abdulrahman Tube  -> On YouTube
client.on('messageCreate', async message => {
  if (message.channel.type === "DM" || message.author.bot) return;
  const arguments = message.content.split(" ");

    var lang = db.get(`${message.guild.id}.lang`);
    if (lang == null || undefined) db.set(`${message.guild.id}`, { lang: "ar" });  
  
  if (message.content === prefix + "bot") {
    var ping = `${Date.now() - message.createdTimestamp}`;
    var api = `${Math.round(client.ws.ping)}`;
    var states = ":green_circle: Excellent";
    var states2 = ":green_circle: Excellent";
    if (admin.includes(message.author.id)) {
      if (Number(ping) > 70) states = ":green_circle: Good";
      if (Number(ping) > 170) states = ":yellow_circle: Not Bad";
      if (Number(ping) > 350) states = ":red_circle: Soo Bad";
      if (Number(api) > 70) states2 = ":green_circle: Good";
      if (Number(api) > 170) states2 = ":yellow_circle: Not Bad";
      if (Number(api) > 350) states2 = ":red_circle: Soo Bad";
      const embed = new MessageEmbed()
        .addField('`My Name`', ` ${client.user.tag}`, true)
        .addField('`My ID`', ` ${client.user.id} `, true)
        .addField('`Developer`', "Abdulrahman Tube", true)
        .addField('Time Taken', ping + " ms :signal_strength: | " + states, true)
        .addField("**WebSocket:**", api + " ms :signal_strength: | " + states2, true)
      message.channel.send({ embeds: [embed] });
    }
  }
  if (message.content.startsWith(prefix + "bc")) {
    if (lang === "ar" || lang === "en") {
      if (!message.member.permissions.has("ADMINISTRATOR")) return;
      if (message.guild.interval) return message.reply('**بث آخر قيد التشغيل ، الرجاء الانتظار حتى ينتهي**')
      let args = arguments.slice(1).join(" ");
      if (!args) return message.reply('**يرجى إرسال رسالة بعد الأمر لإرسالها**');

      message.channel.send(`>>> **[1] جميع الاعضاء\n[2] الاعضاء المتصلين\n[0] الغاء الأمر**`).then(options => {
        const filter = tMsg => tMsg.author.id === message.author.id;

        const chosen = message.channel.createMessageCollector({ filter, time: 60000, max: 1, errors: ["time"] });

        chosen.on('collect', async r => {
          if (r.content === "1") {
            members = Array.from(message.guild.members.cache.values());
            r.delete().catch(err => null);
            options.delete().catch(err => null);
          }
          if (r.content === "2") {
            members = Array.from(message.guild.members.cache.filter(m => m.presence !== null).values());
            r.delete().catch(err => null);
            options.delete().catch(err => null);
          }
          if (r.content == "0") {
            r.delete().catch(err => null);
            options.delete().catch(err => null);
            message.channel.send("**تم الغاء الامر بنجاح**");
          }

          if (['1', '2'].includes(r.content)) {
            if (members == null || members.length == 0) return message.reply('**لا يمنكنني ايجاد الاعضاء**');
            const msgSending = await message.channel.send(`**جاري إرسال الرسالة إلى ${members.length} عضواً...**`)
            var count = 0;
            var ycount = 0;
            var xcount = 0;

            message.guild.interval = await setInterval(() => {
              if (!members[count]) {
                clearInterval(message.guild.inter);

                let emm = new MessageEmbed().setDescription(`** :mailbox_with_mail:  ؛ تم أرسال الرسالة الى  ${ycount} عضواً\n:lock: ؛ و لم أستطع أرسال الرسالة إلى ${xcount} عضواً**`).setTimestamp()

                msgSending.edit({ embeds: [emm] });
                message.guild.interval = false;
              } else if (!members[count].user.bot) {
                members[count].send(`${args} \n <@!${members[count].user.id}>`).then(() => {
                  ycount++;
                }).catch(err => {
                  return xcount++;
                });
              }
              count++;
            }, 500)
          }
        });
      });
    }
  }
  if (message.content.startsWith(prefix + "set-name")) {
    let name = arguments.slice(1).join(" ");
         if (message.author.id !== admin) return;
    if (lang === "en") {
       if (name.length === 1) return message.reply(`My name must be at least 2 characters long :x:.`)
      if (!name) return message.channel.send(`**${prefix}set-name a7mad**`)
      client.user.setUsername(name).catch(err => message.reply(`هذا الاسم مستخدم كثيرا , اكتب اسم آخر من فضلك :x:.`))
      message.channel.send(`My name has been changed to ${name} `)
    } else if (lang === "ar") {
             if (name.length === 1) return message.reply(`My name must be at least 2 characters long :x:.`)
      if (!name) return message.channel.send(`**${prefix}set-name احمد**`)

      client.user.setUsername(name).catch(err => message.reply(`هذا الاسم مستخدم كثيرا , اكتب اسم آخر من فضلك :x:.`))
      message.channel.send(`تم تغيير اسمي الى ${name} ✅.`)
    }
  }
  if (message.content.startsWith(prefix + "set-avatar")) {
    let args = arguments.slice(1).join(" ");
    if (lang == "en") {
      if (!message.author.id === admin) return;
      client.user.setAvatar(args).catch(err => message.reply("Please send a valid url :x:."));
      message.channel.send(`**My avatar has been changed succesfully ✅.**`);
    } else if (lang == "ar") {
      if (!message.author.id === admin) return;
      client.user.setAvatar(args).catch(err => message.reply("ارسل رابط صالح رجائا :x:."));
      message.channel.send(`**تم تغيير صورتي بنجاح ✅.**`);
    }
  }
  if (message.content.startsWith(prefix + 'say')) {
    if (!message.member.permissions.has("ADMINISTRATOR")) return;

    let msg = message.content.split(" ").slice('1').join(" ");
    message.channel.send(msg)
  }
  if (message.content === prefix + "help") {
    if (lang == "ar") {
      let embed = new MessageEmbed()
        .setTitle(`قائمة مساعدة ${client.user.username} 📕`)
        .setDescription(`\`\`\
  ${prefix}bc => ارسال رسالة لأعضاء السيرفر
  ${prefix}set-lang => تغيير اللغة
  ${prefix}say => يكتب نفس الكلام الي تكتبه بعد الامر
  ${prefix}set-avatar => تغيير صورة البوت
  ${prefix}set-name => تغيير اسم البوت
  ${prefix}bot => يظهر معلومات عن البوت\`\`\ `)
      message.reply({ embeds: [embed] })
    } else if (lang === "en") {
      let embed = new MessageEmbed()
        .setTitle(`${client.user.username}'s Help Menu' 📕`)
        .setDescription(`\`\`\
  ${prefix}bc => Sens a message to the server members
  ${prefix}set-lang => Changed the language
  ${prefix}say => Says what you write after the command
  ${prefix}set-avatar => Change the bot's avatar
  ${prefix}set-name => Change the bot's name
  ${prefix}bot => Shows the bot informations\`\`\ `)
      message.reply({ embeds: [embed] })
    }
  }
  if (message.content.startsWith(prefix + "set-lang")) {
    if (!arguments[1]) {
      message.channel.send(`**\`${prefix}set-lang ar\` --> للغة العربية
\`${prefix}set-lang en\` --> English language**`)
    } else if (arguments[1] === "en") {
      db.set(`${message.guild.id}`, {
        lang: "en"
      })
      message.channel.send('**English language selected**')
    } else if (arguments[1] === "ar") {
      db.set(`${message.guild.id}`, {
        lang: "ar"
      })
      message.channel.send('**تم تحديد اللغة العربية**')
    } else if (arguments[1] !== "ar" || "en") {
      var lang = db.get(`${message.guild.id}.lang`)
      if (lang == null || undefined) db.set(`${message.guild.id}`, { lang: "en" })
      if (lang == "ar") {
        message.channel.send(
          `**لا يمكن التعرف على الغة**`
        )
      } else if (lang == "en") {
        message.channel.send(
          `**This language isn't supported**`
        )
      }
    }
  }
});

app.listen(() => console.log(`Bot Braodcast By Alae
`));
