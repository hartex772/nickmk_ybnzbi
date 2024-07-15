const axios = require('axios').default;
const Bottleneck = require('bottleneck');
const express = require('express');
const http = require('http');

class AutoReplier {
    constructor(tokens, channelIds, userIds, messages) {
        this.tokens = tokens;
        this.channelIds = channelIds;
        this.userIds = userIds;
        this.messages = messages;
        this.lastProcessedMessageIds = new Map();
        this.lastCheckedMessageIds = new Map();
        this.limiter = new Bottleneck({ maxConcurrent: tokens.length, minTime: 30 });
        this.session = axios.create();
    }

    async getUserMessages(token, channelId) {
        const userMessages = [];
        try {
            const headers = { 'authorization': token };
            let url = `https://discord.com/api/v10/channels/${channelId}/messages?limit=50`;
            if (this.lastCheckedMessageIds.has(channelId)) {
                url += `&after=${this.lastCheckedMessageIds.get(channelId)}`;
            }
            const response = await this.session.get(url, { headers });
            const messages = response.data;

            for (const message of messages) {
                if (this.userIds.includes(message.author.id) && !this.lastProcessedMessageIds.get(channelId)?.has(message.id)) {
                    userMessages.push({ userId: message.author.id, messageId: message.id, channelId });
                    if (!this.lastProcessedMessageIds.has(channelId)) {
                        this.lastProcessedMessageIds.set(channelId, new Set());
                    }
                    this.lastProcessedMessageIds.get(channelId).add(message.id);
                }
            }

            if (messages.length > 0) {
                this.lastCheckedMessageIds.set(channelId, messages[0].id);
            }
        } catch (error) {
            console.error(`An error occurred while fetching user messages from channel ${channelId}: ${error}`);
        }
        return userMessages;
    }

    async replyToMessage(token, messageId, channelId, retryCount = 0) {
        try {
            const headers = { 'authorization': token };
            await this.session.post(`https://discord.com/api/v10/channels/${channelId}/typing`, {}, { headers });
            const currentMessage = this.messages[Math.floor(Math.random() * this.messages.length)];
            const replyPayload = {
                content: currentMessage,
                message_reference: { message_id: messageId }
            };
            await this.session.post(`https://discord.com/api/v10/channels/${channelId}/messages`, replyPayload, { headers });
            console.log(`Replied to message ID ${messageId} in channel ${channelId} with: ${currentMessage}`);
        } catch (error) {
            console.error(`An error occurred: ${error}`);
            if (error.response && error.response.status === 429) {
                const retryAfter = error.response.headers['retry-after'] || 10;
                console.log(`Rate limited. Retrying in ${retryAfter} seconds...`);
                await new Promise(resolve => setTimeout(resolve, (parseInt(retryAfter) + 1) * 1000));
                await this.replyToMessage(token, messageId, channelId, retryCount);
            } else if (retryCount < 3) {
                console.log('Retrying in 10 seconds...');
                await new Promise(resolve => setTimeout(resolve, 10000));
                await this.replyToMessage(token, messageId, channelId, retryCount + 1);
            } else {
                console.error(`Failed to reply to message ID ${messageId} after 3 attempts.`);
            }
        }
    }

    async replyToMessagesFromAllTokens(messages) {
        const promises = [];
        for (const { messageId, channelId } of messages) {
            const tokenPromises = this.tokens.map(token =>
                this.limiter.schedule(() => this.replyToMessage(token, messageId, channelId))
            );
            promises.push(...tokenPromises);
        }
        await Promise.all(promises);
    }
}

async function replyToUsers(autoReplier) {
    while (true) {
        const allMessages = [];
        for (const token of autoReplier.tokens) {
            for (const channelId of autoReplier.channelIds) {
                const userMessages = await autoReplier.getUserMessages(token, channelId);
                allMessages.push(...userMessages);
            }
        }
        await autoReplier.replyToMessagesFromAllTokens(allMessages);
        await new Promise(resolve => setTimeout(resolve, 50));
    }
}

const tokens = [
    "توكنك",
];
const channelIds = [
    "ايدي الروم"
];
const userIds = [
    "ايدي ولد زبي",
];
const messages = [
  'شقمك',
  'حرقمك',
  'بصعمك', 
  'فتحمك', 
  'بحوي كسم الي جابتك', 
  'ياولد الجرار', 
  'انيك كسمك يبن القحاب', 
  'و ادحش بكسمك زوبر عملاق', 
  'و انيك كسمك بكل زمان', 
  'و اضرب كسمك بكل مكان', 
  'و اشنق كسمك بين الابعاد', 
  'و اعسل كسمك المتناك', 
  'تزبيل كسمك المنكوح',
  'و جعل كسمك قحبه منيوك', 
  'تحليه كسمك', 
  'نيك كسمك', 
  'كسمك', 
  'كسختك', 
  'كسعمتك', 
  'كسخالاتك', 
  'كسخالك', 
  'كسم اسلافك', 
  'كسم احيائك', 
  'كسم اولادك', 
  'نيك كسم ابوك الديوث', 
  'كسخوك', 
  'اشق رحم امك', 
  'كسمك منهار', 
  'كسختك محتاج ل غيار', 
  'تعديه كسمك', 
  'تمعدن كسمك', 
  'تحجر كسختك', 
  'انقلاب كسمك الي قرد', 
  'اضرب كسمك بالسوط', 
  'انيك كسمك علطول', 
  'انيك ربمك يبن المقحبه', 
  'تقلبات كس امك المزاجيه', 
  'اغتيال كس امك من الامام و الخلف', 
  'تلاعب ب جثه امك', 
  'ادخل كسمك للجحيم', 
 'جعلمك تتناك من زنجي', 
 'ادحش بكسمك العود', 
 'اسلق كسمك',
 'اقود طيزمك', 
 'اقرط ربمك', 
 'اخسف عرضمك', 
 'اشلمك', 
 'اعوقمك', 
 'اخنقمك', 
 'اقتلمك', 
 'افعطمك', 
 'اجلطمك', 
 'اقودمك', 
 'نيكمك', 
 'صفعمك', 
 'جلطمك', 
 'خرطمك', 
 'نباحمك', 
 'استفسارمك', 
 'هروبمك', 
 'قشعمك', 
 'غدرمك', 
 'سلخمك', 
 'فسخمك', 
 'نسخمك', 
 'صرعمك', 
 'طخمك ارضا', 
 'ابادتمك', 
 'موتمك جوعا', 
 'تشرد كسمج', 
 'تبول امج تحتها', 
 'خرائمك', 
 'انقراض ربمك يبن العارية', 
 'يبن زبي', 
 'يبن الشرموطة', 
 'يبن المفعوصة', 
 'يبن المقعورة', 
 'يبن المقتولة', 
 'يبن المبعورة', 
 'اغشائمك', 
 'نطح كسمك', 
 'اختفائمك', 
 'ارباكمك', 
 'توترمك', 
 'توسلمك', 
 'تقعبرمك', 
 'تنشفمك', 
 'تخسفمك', 
 'انقصاف عرضمك', 
 'التفاف طيزمج', 
 'ارتقائمك للاعلالي', 
 'انصراعمك يبن الخاري', 
 'تقرجط ترمة امك', 
 'نكح حتشون ربك', 
 'تنشيف خرزت والدتك', 
 'تصفف كسمج', 
 'تشردمك يبن الجحبة', 
 'انصراع زكمك تحت رحمة عيري يبن الفيري', 
 'طلبمك الرحمة', 
 'استنجادمك', 
 'استفزازمك', 
 'يبن المزعجة', 
 'نيكخواتمك', 
 'انطفائمك', 
 'انقهارمك', 
 'انحراقمك', 
 'خرقمك', 
 'تعسفمك', 
 'تخسفمك', 
 'تقشرمك', 
 'تعثرمك', 
 'انفصالمك', 
 'انخصالمك', 
 'انتحار دينمك', 
 'اخضاعمك', 
 'استغلالمك', 
 'تقرطمك', 
 'فشلمك', 
 'تعبمك', 
 'خرطمك', 
 'طحنمك', 
 'دفنمك و هي حية', 
 'نيك امواتمك', 
 'انقطاع نسلمك', 
 'ضرب طيزمك بمنجل يبن تيري', 
 'ربطمك بشجرة', 
 'اقعرمك يبن القوادة', 
 'نيجمك يبن الخنيثة', 
 'كسمين امج يبن اللبوثة', 
 'نيجمك يبن المكبوثة', 
 'قربعة طيزمج', 
 'نحر نسلمج', 
 'قهر طيزمج', 
 'قطع كسمياتمج', 
 'انهيار حتشونمج', 
 'ضربمك يبن البواسر', 
 'انقراض جميع اسلاف ربك يبن المعاقين', 
 'تشرد ابوك ابن الهاربة و تركك وحيدا يبن القحبة', 
 'احتراق منزل ربك يبن الجحوبة', 
 'اختراق كسمج', 
 'افتكاك طيزمج', 
 'شق خواتمك يبن عيري', 
 'فقس عقلمك', 
 'نيك اهلمك', 
 'شوي جدمك', 
 'رفدمك', 
 'رعفمك', 
 'خلقمك', 
 'شوطمك', 
 'حنطمك', 
 'تكريسمك', 
 'تعبيسمك', 
 'تفريممك', 
 'تقليدمك', 
 'تعنيفمك', 
 'تخرفنمك', 
 'تبزيزمك', 
 'تعبمك', 
 'نيكهامك', 
 'نيكاصلمك', 
 'تعوبجمك', 
 'طخمك', 
 'طعنمك', 
 'نفخمك', 
 'صفقمك', 
 'شلقمك', 
 'بكيمك', 
 'حكمك', 
 'ركلمك', 
 'سكسمك', 
 'سفكمك', 
 'تفريشمك', 
 'يبن المكلوبة', 
 'يبن المسعورة', 
 'يبن القواويد', 
 'يبن الداعر', 
 'يبن الساعر', 
 'نيكمك العاهر', 
 'تفتيشمك', 
 'نطحختمك', 
 'تدبيزمك', 
 'تكريسمك', 
 'وفاتمك', 
 'استشهادمك', 
 'تخويطمك', 
 'تعويجمك', 
 'تنشيف نسلمك', 
 'قصاصمك', 
 'هلاك امك', 
 'شعور امك بالراحة', 
 'نيكمك يبن الكلبة', 
 'تشبيع كسمك بوكسات', 
 'تكسير راس امك', 
 'امك تتناك بينما انت تفرح وتصفق',
 'امك تبحث عن زبي',
 'عثور امك على زبي',
 'كسمك فاتن',
 'قصف كسمك بالنووي',
 'سكب الكحول في كسمك',
 'زرع فواكه في كسمك',
 'قطع أعضاء امك بالخنجر',
 'كلك الظلام ينيك كسمك مع ملكة النور',
 'افتتاح مقهى في كسمك',
 'لطم امك لحد التبلل',
 'تأليف كتاب الاصول في نيك امك',
 'خلع الستار عن زبي زبي والتصاقه في كسمك',
 'رش كسمك بالكولا',
 'كس سلالتك لحد ادم ودحش ادم في كسمك',
 'وضع ام اربعة واربعين في كسمك',
 'كسمك كبير وعميق لدرجة ان حتى امك وقاعت وضاعت فيه',
 'كسمك هاوية خاوية',
 'شق كسمك 6191346 نصف',
 'تجول المني بكسمك',
 'زبي بكس امك يا ابن المجهول',
 'خدش سوة ربك', 
 'حنط اهلك', 
 'نيكمك بزب افريقي', 
 'باكا باكا نيكمك يبن الحزاقة', 
 'نيكمك يبن المحنوكة', 
 'شعور امك بالسعادة', 
 'انصداممك', 
 'ذوبانمك', 
 'رفسمك', 
 'قلع ضرس امك', 
 'خبط امك بكف خماسي', 
 'سقلمك', 
 'حقنمك', 
 'عناقمك', 
 'لعقمك', 
 'لحسمك', 
 'فرشمك', 
 'غريمك', 
 'غرق ابوك', 
 'وفاة اخوك', 
 'نيكمك بزب دارك و لحن الفرار', 
 'نايك كسمك و الي مشعل فيه النار', 
 'نيك ربك يبن الجرار', 
 'انيك كسختمك يبن الفار', 
 'فقس ربمك يبن قضيبي', 
 'جري جدمك', 
 'حوي عرضمك', 
 'طحن خواتمك', 
 'تكسير سنمك', 
 'انقطاع نسلمك', 
 'شعور كسمك بالحزن', 
 'انهيار شرف امك', 
 'تعليق سوة امك بالشجرة',  
 'يبن الكل', 
 'يبن العالم', 
 'خشي السواك بطبون امك', 
 'نيك حتشون يماك', 
 'الو عادي انيك امك', 
 'تم كسر ظهر امك', 
 'ادخال الموس بكسمك يبن اللبوس', 
 'تشبيع كسمك بوس', 
 'البزق بطبون امك', 
 'حرق شعر امك', 
 'تكفيتي بطبونمك', 
 'تنزيل المعجزات على راسمك', 
 'خبط كسمك بالباب يبن العطاية', 
 'نيك ربمك يبن الحلاب', 
];
const autoReplier = new AutoReplier(tokens, channelIds, userIds, messages);
replyToUsers(autoReplier);

const app = express();
const server = http.createServer(app);

app.get('/', (req, res) => {
    res.send(`
        <body>
            <center><h1>كسمك يا علاوي</h1></center>
        </body>
    `);
});

app.get('/webview', (req, res) => {
    res.setHeader('Content-Type', 'text/html');
    res.send(`
        <html>
            <head>
                <title>كسمك يا لحن</title>
            </head>
            <body style="margin: 0; padding: 0;">
                <iframe width="100%" height="100%" src="https://axocoder.vercel.app/" frameborder="0" allowfullscreen></iframe>
            </body>
        </html>
    `);
});

server.listen(8080, () => {
    console.log("I'm ready to nik ksm 3lawi..!");
    console.log('Im ready to nik ksm l7n..!');
});
