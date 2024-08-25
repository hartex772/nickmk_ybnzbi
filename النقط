const axios = require('axios');
const express = require('express');
const http = require('http');

const app = express();
const server = http.createServer(app);

const tokens = [
  "توكنك"
];
const roomIds = [
  'ايدي رب الروم'
];
const triggerWords = [
  'نقط',
  'النقطة',
  'نقطة',
  'تنقيط',
  '.',
  'نقطه',
  'النقطه'
];
const responses = [
  'انقط بكسمك',
  'احط النقطة بكسخواتمك',
  'حرق كسمك يبن النقط'
];
const userIdsToRespond = [
  'ايدي ابن لكحبو'
];

async function monitorMessages(token) {
  for (const roomId of roomIds) {
    monitorChannel(token, roomId);
  }
}

async function monitorChannel(token, channelId) {
  let lastMessageId = null;

  while (true) {
    try {
      const response = await axios.get(`https://discord.com/api/v10/channels/${channelId}/messages`, {
        headers: {
          'Authorization': token
        },
        params: {
          limit: 1,
          after: lastMessageId,
        }
      });if (response.data.length > 0) {
        const message = response.data[0];
        lastMessageId = message.id;

        if (!message.author.bot && userIdsToRespond.includes(message.author.id)) {
          for (let word of triggerWords) {
            if (message.content.includes(word)) {
              sendMessage(token, channelId, getRandomResponse(), message.id);
              break;
            }
          }
        }
      }
    } catch (error) {
      console.error('Error fetching messages:', error);
    }

    await new Promise(resolve => setTimeout(resolve, 200)); 
  }
}

function getRandomResponse() {
  return responses[Math.floor(Math.random() * responses.length)];
}

async function sendMessage(token, channelId, content, messageId) {
  try {
    await axios.post(`https://discord.com/api/v10/channels/${channelId}/messages`, {
      content: content,
      message_reference: {
        message_id: messageId,
      },
    }, {
      headers: {
        'Authorization': token,
        'Content-Type': 'application/json',
      },
    });
  } catch (error) {
    console.error('Error sending message:', error);
  }
}

tokens.forEach(token => {
  monitorMessages(token).then(() => console.log(`Started monitoring messages for token: ${token}`));
});

app.get('/', (req, res) => {
  res.send(`<body><center><h1>كسمك يا علاوي</h1></center></body>`);
});

app.get('/webview', (req, res) => {
  res.setHeader('Content-Type', 'text/html');
  res.send(`
    <html>
      <head>
        <title>ام علاوي</title>
      </head>
      <body style="margin: 0; padding: 0;">
        <iframe width="100%" height="100%" src="https://axocoder.vercel.app/" frameborder="0" allowfullscreen></iframe>
      </body>
    </html>
  `);
});

server.listen(8080, () => {
  console.log("im ready to nik ksm 3lawi!!");
});
