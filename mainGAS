function run() {
  let createLastTime = getLastCheckedTime();
  getPagesFromNotion(createLastTime);
}

// 事前に取得したNotionのデータベースIDを入れる
const DATABASE_ID = ""
 
// 事前に取得したNotionのシークレットを入れる
const NOTION_API_KEY = "" 
 
// 事前に取得したchatGPTのAPIkeyを入れる
const OPENAI_API_KEY = ""

function getPagesFromNotion(lastCheckedTime) {
  const url = `https://api.notion.com/v1/databases/${DATABASE_ID}/query`;

  const options = {
    method: 'post',
    headers: {
      'Authorization': `Bearer ${NOTION_API_KEY}`,
      'Content-Type': 'application/json',
      'Notion-Version': "2022-06-28"
    },
    payload: JSON.stringify({
      filter: {
        property: "Created time",
        created_time: {
          after: lastCheckedTime
        }
      }
    }),
    sorts: [
      {
        property: "Created time",
        direction: "ascending"
      }
    ],
    muteHttpExceptions: true
  };

  try {
    const response = UrlFetchApp.fetch(url, options);
    const data = JSON.parse(response.getContentText());

    if (data.results.length > 0) {
      // console.log(data.results)

      data.results.sort((a, b) => {
        return new Date(a.properties['Created time'].created_time) - new Date(b.properties['Created time'].created_time);
      });

      // data.results.forEach(page => {
      //   console.log(page.properties["単語"].title[0].plain_text);
      // });
      

      data.results.forEach(page => {
        let pageId = page.id;
        let text = page.properties['単語'].title[0].text.content

        let meaning = '「' + text + '」という単語の主な意味を簡潔に挙げてください。ただし、３つ以内とする;'
        let translageText = generateExampleSentence(text, meaning);

        let created_time = page.properties['Created time'].created_time
        const properties = {
          '意味': { // 例: 'Name' というタイトルのプロパティを更新
            'rich_text': [
              {
                'text': {
                  'content': translageText
                }
              }
            ]
          }
        }

        updateNotionPageProperties(pageId, properties);

        let prompt = '「' + text + '」という単語を使って下記のフォーマットで簡単で短めの英語の例文とその後ろに()でその日本語翻訳を１つだけ作成してください。必ず１つだけにしてください。'
        let example = generateExampleSentence(text, prompt);

        const exampleText = {
          '例文': { // 例: 'Name' というタイトルのプロパティを更新
            'rich_text': [
              {
                'text': {
                  'content': example
                }
              }
            ]
          }
        }
        updateNotionPageProperties(pageId, exampleText);

        let etymology = '「' + text + '」という単語の語源を解説して。解説はできるだけ短く50語以内に解説して;'
        let etymologyText = generateExampleSentence(text, etymology);

        const etymologyInput = {
          '語源': { // 例: 'Name' というタイトルのプロパティを更新
            'rich_text': [
              {
                'text': {
                  'content': etymologyText
                }
              }
            ]
          }
        }
        updateNotionPageProperties(pageId, etymologyInput);

        saveLastCheckedTime(created_time)
      });
    } else {
      console.log("データは存在しませんでした");
    }
  } catch (error) {
    console.error('Error fetching pages from Notion:', error);
  }
}



function updateNotionPageProperties(pageId, properties) {
  const url = `https://api.notion.com/v1/pages/${pageId}`;

  const options = {
    method: 'patch',
    headers: {
      'Authorization': `Bearer ${NOTION_API_KEY}`,
      'Content-Type': 'application/json',
      'Notion-Version': "2022-06-28"
    },
    payload: JSON.stringify({
      properties: properties
    }),
    muteHttpExceptions: true
  };

  try {
    const response = UrlFetchApp.fetch(url, options);
    const jsonResponse = JSON.parse(response.getContentText());
    // console.log(jsonResponse);
  } catch (error) {
    console.error('Error updating Notion page:', error);
  }
}

function generateExampleSentence(word, prompt) {
  const url = "https://api.openai.com/v1/chat/completions";
  
  const apiUrl = 'https://api.openai.com/v1/chat/completions';

  const messages = [
    { 'role': 'system', 'content': prompt },
    { 'role': 'user', 'content': prompt },
  ];

  const headers = {
    'Authorization': 'Bearer ' + OPENAI_API_KEY,
    'Content-type': 'application/json',
    'X-Slack-No-Retry': 1
  };
  //オプションの設定(モデルやトークン上限、プロンプト)
  const options = {
    'muteHttpExceptions': true,
    'headers': headers,
    'method': 'POST',
    'payload': JSON.stringify({
      'model': 'gpt-3.5-turbo',
      'max_tokens': 2000,
      'temperature': 0.9,
      'messages': messages
    })
  };
  //OpenAIのChatGPTにAPIリクエストを送り、結果を変数に格納
  const response = JSON.parse(UrlFetchApp.fetch(apiUrl, options).getContentText());
  //ChatGPTのAPIレスポンスをログ出力
  // console.log(response.choices[0].message.content);
  return response.choices[0].message.content;
}

function getLastCheckedTime() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const lastCheckedTime = sheet.getRange('A1').getValue();
  return lastCheckedTime;
}

function saveLastCheckedTime(time) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  sheet.getRange('A1').setValue(time);
}


