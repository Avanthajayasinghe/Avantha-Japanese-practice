<!DOCTYPE html>
<html lang="si">
<head>
  <meta charset="UTF-8">
  <title>JLPT Kanji Quiz</title>
  <style>
    body {
      font-family: 'Noto Sans Sinhala', sans-serif;
      max-width: 800px;
      margin: 30px auto;
      padding: 20px;
      border: 2px solid #444;
      border-radius: 8px;
      background: #fafafa;
      text-align: center;
    }
    h2,h3 { margin: 10px 0; }
    #levelMenu, #partMenu, #quizArea, #finalResultArea { display: none; }
    .optionBtn {
      display: block;
      width: 80%;
      margin: 10px auto;
      padding: 12px;
      font-size: 22px;
      border: 2px solid #666;
      border-radius: 6px;
      background: white;
      cursor: pointer;
    }
    .optionBtn:hover { background: #eee; }
    #kanjiBox {
      font-size: 160px;
      border: 2px solid #333;
      margin: 20px 0;
      padding: 10px;
      user-select: none;
    }
    #scoreBox {
      font-size: 20px;
      margin-bottom: 10px;
      font-weight: bold;
    }
    #resultMsg {
      font-size: 22px;
      font-weight: bold;
      margin-top: 15px;
      min-height: 30px;
    }
    #restartBtn {
      margin-top: 20px;
      padding: 10px 20px;
      font-size: 18px;
      background: #2196F3;
      color: white;
      border: none;
      border-radius: 6px;
      cursor: pointer;
    }
  </style>
</head>
<body>

  <h2>JLPT Kanji Quiz</h2>

  <!-- Level Select -->
  <div id="levelMenu">
    <h3>ඔබට අවශ්‍ය Level එක තෝරන්න</h3>
    <button onclick="chooseLevel('N5')">N5 (103 Kanji)</button>
    <button onclick="chooseLevel('N4')">N4 (181 Kanji)</button>
    <button onclick="chooseLevel('N3')">N3 (363 Kanji)</button>
  </div>

  <!-- Part + Question Menu -->
  <div id="partMenu">
    <h3 id="levelTitle"></h3>
    <div id="parts"></div>
    <h3>ප්‍රශ්න ගණන තෝරන්න</h3>
    <select id="questionCount">
      <option value="5">5</option>
      <option value="10" selected>10</option>
      <option value="20">20</option>
      <option value="all">සියල්ල</option>
    </select>
    <br><br>
    <button onclick="startQuiz()">🚀 Start Quiz</button>
  </div>

  <!-- Quiz -->
  <div id="quizArea">
    <div id="scoreBox">Score: 0 / 0</div>
    <div id="kanjiBox">一</div>
    <div id="options"></div>
    <div id="resultMsg"></div>
  </div>

  <!-- Final Result -->
  <div id="finalResultArea">
    <h3>අවසාන ප්‍රතිඵල</h3>
    <div id="finalResult"></div>
    <button id="restartBtn" onclick="showLevelMenu()">🔄 නැවත අරඹන්න</button>
  </div>

<script>
  // 📌 JLPT Kanji datasets
  const kanjiSets = {
   N5: [
  {kanji:"一", meaning:"එක"},
  {kanji:"二", meaning:"දෙක"},
  {kanji:"三", meaning:"තුන"},
  {kanji:"四", meaning:"හතර"},
  {kanji:"五", meaning:"පහ"},
  {kanji:"六", meaning:"හය"},
  {kanji:"七", meaning:"හත"},
  {kanji:"八", meaning:"අට"},
  {kanji:"九", meaning:"නවය"},
  {kanji:"十", meaning:"දහය"},
  {kanji:"百", meaning:"සියය"},
  {kanji:"千", meaning:"දහස"},
  {kanji:"万", meaning:"දශසහස"},
  {kanji:"円", meaning:"යෙන් / මුදල්"},
  {kanji:"上", meaning:"ඉහළ"},
  {kanji:"下", meaning:"පහළ"},
  {kanji:"中", meaning:"මැද / තුල"},
  {kanji:"大", meaning:"ලොකු"},
  {kanji:"小", meaning:"කුඩා"},
  {kanji:"月", meaning:"සඳ / මාසය"},
  {kanji:"日", meaning:"දවස / සූරිය"},
  {kanji:"年", meaning:"අවුරුද්ද"},
  {kanji:"時", meaning:"වේලාව"},
  {kanji:"分", meaning:"මිනිත්තු / කොටස"},
  {kanji:"今", meaning:"දැන්"},
  {kanji:"何", meaning:"මොනවාද"},
  {kanji:"先", meaning:"ඉදිරිය / පෙර"},
  {kanji:"毎", meaning:"සෑම / හැම"},
  {kanji:"午", meaning:"මධ්‍යහ්නය"},
  {kanji:"前", meaning:"ඉදිරිය / පෙර"},
  {kanji:"後", meaning:"පසු / පසෙක"},
  {kanji:"間", meaning:"අතර / අතරමං"},
  {kanji:"東", meaning:"නැගෙනහිර"},
  {kanji:"西", meaning:"බටහිර"},
  {kanji:"南", meaning:"දකුණ"},
  {kanji:"北", meaning:"උතුර"},
  {kanji:"口", meaning:"මූණ / තොට"},
  {kanji:"目", meaning:"ඇස"},
  {kanji:"耳", meaning:"කන"},
  {kanji:"手", meaning:"අත"},
  {kanji:"足", meaning:"පා"},
  {kanji:"力", meaning:"බලය"},
  {kanji:"男", meaning:"පුරුෂ"},
  {kanji:"女", meaning:"ගැහැණු"},
  {kanji:"子", meaning:"ළමය"},
  {kanji:"父", meaning:"තාත්තා"},
  {kanji:"母", meaning:"අම්මා"},
  {kanji:"友", meaning:"යාලුවා"},
  {kanji:"人", meaning:"මිනිසා"},
  {kanji:"名", meaning:"නම"},
  {kanji:"先生", meaning:"ගුරු"},
  {kanji:"先週", meaning:"පසුගිය සතිය"},
  {kanji:"来週", meaning:"ඉදිරි සතිය"},
  {kanji:"学校", meaning:"පාසල"},
  {kanji:"生", meaning:"ජීවිතය / සිසු"},
  {kanji:"学", meaning:"ඉගෙනීම"},
  {kanji:"校", meaning:"පාසල"},
  {kanji:"入", meaning:"ඇතුල් වීම"},
  {kanji:"出", meaning:"පිටවීම"},
  {kanji:"行", meaning:"යනවා"},
  {kanji:"来", meaning:"එනවා"},
  {kanji:"帰", meaning:"ආපසු යනවා"},
  {kanji:"見", meaning:"බලනවා"},
  {kanji:"聞", meaning:"අහනවා"},
  {kanji:"話", meaning:"කතා කරනවා"},
  {kanji:"言", meaning:"කියනවා"},
  {kanji:"読", meaning:"කියවනවා"},
  {kanji:"書", meaning:"ලියනවා"},
  {kanji:"食", meaning:"කනවා"},
  {kanji:"飲", meaning:"බොනවා"},
  {kanji:"買", meaning:"ගන්නවා"},
  {kanji:"売", meaning:"විකිණනවා"},
  {kanji:"休", meaning:"විවේකය"},
  {kanji:"車", meaning:"රථය / කාරය"},
  {kanji:"駅", meaning:"රෙල් ගේට්ටුව"},
  {kanji:"道", meaning:"මාර්ගය"},
  {kanji:"山", meaning:"කන්ද"},
  {kanji:"川", meaning:"ගඟ"},
  {kanji:"雨", meaning:"වැස්ස"},
  {kanji:"天", meaning:"ඉහළ / දේව"},
  {kanji:"空", meaning:"අහස / හිස්"},
  {kanji:"火", meaning:"ගින්න"},
  {kanji:"水", meaning:"වතුර"},
  {kanji:"木", meaning:"ගස / ලී"},
  {kanji:"金", meaning:"මුදල් / රිදී"},
  {kanji:"土", meaning:"පස"},
  {kanji:"花", meaning:"මල"},
  {kanji:"草", meaning:"ඇල / ගස් කොළ"},
  {kanji:"竹", meaning:"බඹර ගස්"},
  {kanji:"糸", meaning:"නූල්"},
  {kanji:"米", meaning:"අරහ"},
  {kanji:"貝", meaning:"ගවියා"},
  {kanji:"石", meaning:"ගල්"},
  {kanji:"犬", meaning:"බල්ලා"},
  {kanji:"魚", meaning:"මාළු"},
  {kanji:"鳥", meaning:"කුරුල්ලා"},
  {kanji:"馬", meaning:"අශ්වය"},
  {kanji:"駅", meaning:"ස්ථානය"},
  {kanji:"社", meaning:"සමාගම / ආයතනය"},
  {kanji:"電", meaning:"විදුලිය"},
  {kanji:"語", meaning:"භාෂාව"},
  {kanji:"国", meaning:"රට"},
  {kanji:"円", meaning:"යෙන්"},
  {kanji:"白", meaning:"සුදු"},
  {kanji:"赤", meaning:"රතු"},
  {kanji:"青", meaning:"නිල්"},
  {kanji:"黒", meaning:"කලු"},
  {kanji:"上手", meaning:"හොඳින් කරනවා"},
  {kanji:"下手", meaning:"අඩු / හොඳ නැති"},
],
    N4: [
  {kanji:"不", meaning:"නැති / නො-"},
  {kanji:"世", meaning:"ලෝකය"},
  {kanji:"主", meaning:"ප්‍රධාන / අයිතිකරු"},
  {kanji:"事", meaning:"කාරණය / දෙය"},
  {kanji:"仕", meaning:"සේවය / සේවකයෙක්"},
  {kanji:"代", meaning:"යුගය / වෙනුවට"},
  {kanji:"住", meaning:"පදිංචි වීම"},
  {kanji:"体", meaning:"ශරීරය"},
  {kanji:"作", meaning:"කරනවා / නිර්මාණය"},
  {kanji:"使", meaning:"භාවිතා කරනවා"},
  {kanji:"便", meaning:"සුලභය / සේවාව"},
  {kanji:"働", meaning:"වැඩ කරනවා"},
  {kanji:"元", meaning:"මුල් / මූලික"},
  {kanji:"兄", meaning:"අයියා"},
  {kanji:"光", meaning:"ආලෝකය"},
  {kanji:"写", meaning:"පිටපත් කරනවා"},
  {kanji:"冬", meaning:"ශීතකාලය"},
  {kanji:"切", meaning:"කපනවා"},
  {kanji:"別", meaning:"වෙනස්"},
  {kanji:"力", meaning:"බලය"},
  {kanji:"動", meaning:"චලනය"},
  {kanji:"医", meaning:"වෛද්‍ය"},
  {kanji:"去", meaning:"ගිය / අතීතය"},
  {kanji:"味", meaning:"රසය"},
  {kanji:"命", meaning:"ජීවිතය"},
  {kanji:"品", meaning:"භාණ්ඩ"},
  {kanji:"員", meaning:"සාමාජිකයෙක්"},
  {kanji:"問", meaning:"ප්‍රශ්නය"},
  {kanji:"図", meaning:"ඉරියව් / සිතියම"},
  {kanji:"地", meaning:"බිම / පසුබිම"},
  {kanji:"堂", meaning:"මහලු ගොඩනැගිල්ල"},
  {kanji:"場", meaning:"ස්ථානය"},
  {kanji:"声", meaning:"හඬ"},
  {kanji:"売", meaning:"විකුණනවා"},
  {kanji:"夏", meaning:"ගිම්හාන"},
  {kanji:"夕", meaning:"සඳු"},
  {kanji:"夜", meaning:"රාත්‍රිය"},
  {kanji:"太", meaning:"මහත් / තද"},
  {kanji:"好", meaning:"කැමැත්ත / ප්‍රිය"},
  {kanji:"妹", meaning:"නිවාසිය / ගිගිරි"},
  {kanji:"姉", meaning:"අක්කා"},
  {kanji:"始", meaning:"ආරම්භය"},
  {kanji:"字", meaning:"අකුර"},
  {kanji:"室", meaning:"කාමරය"},
  {kanji:"家", meaning:"ගෙදර"},
  {kanji:"寒", meaning:"සීතල"},
  {kanji:"少", meaning:"අඩු / ටික"},
  {kanji:"屋", meaning:"ගොඩනැගිල්ල / වෙළඳසැල"},
  {kanji:"工", meaning:"අවිය / කාර්මික"},
  {kanji:"市", meaning:"නගරය / වෙළඳපොළ"},
  {kanji:"帰", meaning:"ආපසු යන්න"},
  {kanji:"広", meaning:"විශාල / පළල"},
  {kanji:"度", meaning:"අවස්ථාව / උෂ්ණත්වය"},
  {kanji:"建", meaning:"ගොඩනගනවා"},
  {kanji:"引", meaning:"ඇදීම"},
  {kanji:"弟", meaning:"මල්ලි"},
  {kanji:"弱", meaning:"දුර්වල"},
  {kanji:"強", meaning:"ශක්තිමත්"},
  {kanji:"待", meaning:"මග බලනවා"},
  {kanji:"心", meaning:"හදවත / සිත"},
  {kanji:"思", meaning:"සිතනවා"},
  {kanji:"急", meaning:"හදිසි / ඉක්මන්"},
  {kanji:"悪", meaning:"නරක"},
  {kanji:"意", meaning:"අර්ථය / අදහස"},
  {kanji:"所", meaning:"ස්ථානය"},
  {kanji:"持", meaning:"රඳවා ගන්නවා"},
  {kanji:"教", meaning:"ඉගැන්වීම"},
  {kanji:"文", meaning:"වචනය / ලිපිය"},
  {kanji:"旅", meaning:"ගමන"},
  {kanji:"春", meaning:"වසන්තය"},
  {kanji:"昼", meaning:"දවාලේ"},
  {kanji:"暑", meaning:"උණුසුම්"},
  {kanji:"暗", meaning:"අඳුරු"},
  {kanji:"曜", meaning:"සති දිනය"},
  {kanji:"有", meaning:"අතින් / තිබෙනවා"},
  {kanji:"服", meaning:"ඇඳුම"},
  {kanji:"朝", meaning:"උදේ"},
  {kanji:"村", meaning:"ගම"},
  {kanji:"林", meaning:"ගස් පොකුණ"},
  {kanji:"森", meaning:"වනය"},
  {kanji:"楽", meaning:"සන්තෝෂය / සංගීතය"},
  {kanji:"歌", meaning:"ගීතය"},
  {kanji:"止", meaning:"නවත්වනවා"},
  {kanji:"正", meaning:"හරි / නිවැරදි"},
  {kanji:"歩", meaning:"ඇවිදින්න"},
  {kanji:"死", meaning:"මරණය"},
  {kanji:"民", meaning:"ජනතාව"},
  {kanji:"池", meaning:"වැව"},
  {kanji:"注", meaning:"වතුර දැමීම / නෝට් කිරීම"},
  {kanji:"洋", meaning:"බටහිර"},
  {kanji:"海", meaning:"මුහුද"},
  {kanji:"漢", meaning:"චීන / කන්ජි"},
  {kanji:"牛", meaning:"ගොයියා"},
  {kanji:"物", meaning:"වස්තුව"},
  {kanji:"特", meaning:"විශේෂ"},
  {kanji:"犬", meaning:"බල්ලා"},
  {kanji:"理", meaning:"කාරණය / විද්‍යාව"},
  {kanji:"用", meaning:"භාවිතය"},
  {kanji:"田", meaning:"වගා බිම"},
  {kanji:"町", meaning:"ගම / නගරය"},
  {kanji:"画", meaning:"චිත්‍රය / රූපය"},
  {kanji:"界", meaning:"ලෝකය"},
  {kanji:"病", meaning:"අසනීපය"},
  {kanji:"発", meaning:"ඇරඹීම / සංවර්ධනය"},
  {kanji:"県", meaning:"පළාත"},
  {kanji:"真", meaning:"සත්‍ය / නියම"},
  {kanji:"知", meaning:"දැනුම"},
  {kanji:"短", meaning:"කෙටි"},
  {kanji:"研", meaning:"ඉගෙන ගන්නවා"},
  {kanji:"究", meaning:"පර්යේෂණය"},
  {kanji:"紙", meaning:"කඩදාසිය"},
  {kanji:"終", meaning:"අවසානය"},
  {kanji:"習", meaning:"අධ්‍යයනය"},
  {kanji:"考", meaning:"සිතනවා"},
  {kanji:"者", meaning:"පුද්ගලයෙක්"},
  {kanji:"肉", meaning:"මාංශය"},
  {kanji:"自", meaning:"ස්වයං"},
  {kanji:"色", meaning:"වර්ණය"},
  {kanji:"英", meaning:"ඉංග්‍රීසි / විශිෂ්ට"},
  {kanji:"茶", meaning:"තේ"},
  {kanji:"親", meaning:"මාපියන්"},
  {kanji:"計", meaning:"ඇඳීම / සැලසුම"},
  {kanji:"試", meaning:"පරීක්ෂාව"},
  {kanji:"貸", meaning:"අරිනවා"},
  {kanji:"質", meaning:"ගුණය / තත්ත්වය"},
  {kanji:"赤", meaning:"රතු"},
  {kanji:"走", meaning:"දුවනවා"},
  {kanji:"起", meaning:"එළිදැක්වීම / ඇහැරීම"},
  {kanji:"足", meaning:"පා"},
  {kanji:"転", meaning:"තුරුළු වෙන්න / හැරවීම"},
  {kanji:"軽", meaning:"හැදි"},
  {kanji:"近", meaning:"ආසන්න"},
  {kanji:"送", meaning:"යැවීම"},
  {kanji:"通", meaning:"ඇවිදින්න / ගමන්"},
  {kanji:"進", meaning:"ඉදිරියට යන්න"},
  {kanji:"運", meaning:"ඇනවුම / ගෙන යනවා"},
  {kanji:"遠", meaning:"දුර"},
  {kanji:"都", meaning:"රාජධානිය"},
  {kanji:"重", meaning:"බර"},
  {kanji:"野", meaning:"පිටිය / වල්"},
  {kanji:"銀", meaning:"රිදී"},
  {kanji:"開", meaning:"ඔපනවා"},
  {kanji:"院", meaning:"ආයතනය / රෝහල"},
  {kanji:"集", meaning:"එකතු කරනවා"},
  {kanji:"青", meaning:"නිල්"},
  {kanji:"音", meaning:"ශබ්දය"},
  {kanji:"頭", meaning:"හිස"},
  {kanji:"顔", meaning:"මුහුණ"},
  {kanji:"題", meaning:"විෂය / ප්‍රශ්නය"},
  {kanji:"風", meaning:"සුළඟ"},
  {kanji:"飯", meaning:"ඇත් බත"},
  {kanji:"館", meaning:"ගොඩනැගිල්ල / මාලිගාව"},
  {kanji:"首", meaning:"ගෙඩි / ගිලිය"},
  {kanji:"黄", meaning:"කහ"},
  {kanji:"黒", meaning:"කලු"},
  {kanji:"鳥", meaning:"කුරුල්ලා"},
  {kanji:"魚", meaning:"මාළු"}
// 👉 N4 එකට 181 Kanji
    ],
    N3: [
  {kanji:"両", meaning:"අත් දෙක / දෙපැත්ත"},
  {kanji:"丸", meaning:"වට"},
  {kanji:"交", meaning:"මාරු කරනවා"},
  {kanji:"京", meaning:"රාජධානි (කියෝටෝ / ටෝකියෝ)"},
  {kanji:"仕", meaning:"කාර්යය / සේවය"},
  {kanji:"他", meaning:"වෙනෙක් / වෙනත්"},
  {kanji:"付", meaning:"ඇමතුම් / සම්බන්ධ"},
  {kanji:"令", meaning:"නියෝගය / නීතිය"},
  {kanji:"以", meaning:"අනුව / මත"},
  {kanji:"低", meaning:"අඩු"},
  {kanji:"住", meaning:"පදිංචිය"},
  {kanji:"仲", meaning:"ආශ්‍රය / සම්බන්ධය"},
  {kanji:"伝", meaning:"සංවෘතය / ගෙනයාම"},
  {kanji:"位", meaning:"ස්ථානය / තත්ත්වය"},
  {kanji:"例", meaning:"උදාහරණය"},
  {kanji:"係", meaning:"අධිකාරිය / සම්බන්ධය"},
  {kanji:"信", meaning:"විශ්වාසය / විශ්වාස කරනවා"},
  {kanji:"倉", meaning:"ගබඩාව"},
  {kanji:"候", meaning:"කාලගුණය / සූචකය"},
  {kanji:"借", meaning:"අරිනවා"},
  {kanji:"停", meaning:"නවත්වනවා"},
  {kanji:"健", meaning:"ආරෝග්‍යය"},
  {kanji:"側", meaning:"පැත්ත"},
  {kanji:"働", meaning:"වැඩ කරනවා"},
  {kanji:"億", meaning:"සෙර (100,000,000)"},
  {kanji:"兆", meaning:"ලක්ෂණය / තිලිණය"},
  {kanji:"児", meaning:"ළමය"},
  {kanji:"共", meaning:"එකතු / එකට"},
  {kanji:"兵", meaning:"සෙබළු / යුද්ධය"},
  {kanji:"典", meaning:"මූලය / පොත"},
  {kanji:"冷", meaning:"සීතල"},
  {kanji:"初", meaning:"මුල් / පළමු"},
  {kanji:"別", meaning:"වෙනස් / වෙනම"},
  {kanji:"利", meaning:"ලාභය / සුළු"},
  {kanji:"刷", meaning:"මුද්‍රණය"},
  {kanji:"副", meaning:"උප / ද්විතීය"},
  {kanji:"功", meaning:"විජයය / සාර්ථකත්වය"},
  {kanji:"加", meaning:"එක් කරනවා"},
  {kanji:"努", meaning:"උත්සාහය"},
  {kanji:"労", meaning:"කම්කරුවන් / ප শ্রমය"},
  {kanji:"効", meaning:"ප්‍රයෝජනය / බලපෑම"},
  {kanji:"勇", meaning:"ධෛර්යය"},
  {kanji:"包", meaning:"පැකේජය / සමුදය"},
  {kanji:"卒", meaning:"උපාධිය / ග්‍රාජු"},
  {kanji:"協", meaning:"සහයෝගය"},
  {kanji:"単", meaning:"තනි / සරල"},
  {kanji:"博", meaning:"විශාල / විද්‍යාගාර"},
  {kanji:"印", meaning:"මුද්‍රාව"},
  {kanji:"参", meaning:"සම්බන්ධ වෙන්න / පංති"},
  {kanji:"史", meaning:"ඉතිහාසය"},
  {kanji:"各", meaning:"එකෝ එක්"},
  {kanji:"告", meaning:"දැනුම් දීම"},
  {kanji:"周", meaning:"අසල / වටා"},
  {kanji:"命", meaning:"ජීවිතය / නියෝගය"},
  {kanji:"和", meaning:"ජපන් / සමාධානය"},
  {kanji:"固", meaning:"ඇඳුම / තද"},
  {kanji:"国", meaning:"රට"},
  {kanji:"園", meaning:"ඔට්ටු / උද්‍යානය"},
  {kanji:"堂", meaning:"ගොඩනැගිල්ල / ශාලාව"},
  {kanji:"圧", meaning:"පීඩනය"},
  {kanji:"在", meaning:"තිබෙනවා / පවතිනවා"},
  {kanji:"均", meaning:"සම / සමාන"},
  {kanji:"基", meaning:"මූලය / පදනම"},
  {kanji:"報", meaning:"වාර්තාව"},
  {kanji:"境", meaning:"සීමාව / සන්ධිය"},
  {kanji:"墓", meaning:"සොහොන"},
  {kanji:"増", meaning:"වැඩි කරනවා"},
  {kanji:"変", meaning:"වෙනස් කරනවා"},
  {kanji:"夢", meaning:"කල්පනා / සිහිනය"},
  {kanji:"妻", meaning:"බිරිඳ"},
  {kanji:"婦", meaning:"ගෘහණිය"},
  {kanji:"容", meaning:"දේහය / හැඩය"},
  {kanji:"宿", meaning:"ඇතැම් / නවාතැන්"},
  {kanji:"寄", meaning:"ආසන්නව යන්න / දානය"},
  {kanji:"富", meaning:"ඇශාඛ්‍ය / සම්පත්"},
  {kanji:"寒", meaning:"සිසිල්"},
  {kanji:"察", meaning:"පරීක්ෂාව"},
  {kanji:"対", meaning:"විරුද්ධ / සම්බන්ධ"},
  {kanji:"局", meaning:"කාර්යාලය"},
  {kanji:"居", meaning:"පදිංචි"},
  {kanji:"島", meaning:"දූපත"},
  {kanji:"帳", meaning:"පොත / ලේඛනය"},
  {kanji:"平", meaning:"සම"},
  {kanji:"幸", meaning:"සාන්තුව / සතුට"},
  {kanji:"度", meaning:"අවස්ථාව"},
  {kanji:"庫", meaning:"ගබඩාව"},
  {kanji:"庭", meaning:"ඔය / වත්ත"},
  {kanji:"式", meaning:"ආකෘතිය / උත්සවය"},
  {kanji:"引", meaning:"ඇදීම / අඩු කිරීම"},
  {kanji:"当", meaning:"අදාළ / ගැලපෙන"},
  {kanji:"形", meaning:"හැඩය"},
  {kanji:"役", meaning:"කාර්යය / භූමිකාව"},
  {kanji:"彼", meaning:"ඔහු / යාලුවා"},
  {kanji:"往", meaning:"යාම / ගමන්"},
  {kanji:"得", meaning:"ලැබීම / ලබා ගන්නවා"},
  {kanji:"必", meaning:"නිශ්චිත / අනිවාර්ය"},
  {kanji:"忘", meaning:"අමතක කරනවා"},
  {kanji:"念", meaning:"කල්පනා / සිත"},
  {kanji:"愛", meaning:"ආදරය"},
  {kanji:"感", meaning:"අනූභවය / හැඟීම"},
  {kanji:"態", meaning:"අවස්ථාව / තත්ත්වය"},
  {kanji:"慣", meaning:"අවුරුදු වෙලා / පුරුදු"},
  {kanji:"成", meaning:"වීම / සාර්ථකවීම"},
  {kanji:"戦", meaning:"සටන"},
  {kanji:"戸", meaning:"වෑල්ලි / දොර"},
  {kanji:"所", meaning:"ස්ථානය"},
  {kanji:"払", meaning:"ගෙවීම / පිටවීම"},
  {kanji:"投", meaning:"ගැසීම / 投げる"},
  {kanji:"折", meaning:"කඩනවා / වාදනවා"},
  {kanji:"技", meaning:"කෞශලය"},
  {kanji:"招", meaning:"ඇරයුම / පිළිගැනීම"},
  {kanji:"打", meaning:"මැරීම / වාදනවා"},
  {kanji:"拾", meaning:"ගන්නවා / රැස් කරනවා"},
  {kanji:"指", meaning:"ඇඟිල්ල / යොමු කරනවා"},
  {kanji:"放", meaning:"ඉවත් කරනවා / නිදහස්"},
  {kanji:"敗", meaning:"පරාජය"},
  {kanji:"散", meaning:"චිත්‍රය / බෙදනවා"},
  {kanji:"数", meaning:"ගණන"},
  {kanji:"整", meaning:"රැස් කරනවා / සකස් කරනවා"},
  {kanji:"旅", meaning:"ගමන"},
  {kanji:"族", meaning:"කුලය / පවුල"},
  {kanji:"昔", meaning:"පැරණි / කලින්"},
  {kanji:"昭", meaning:"ශෝවා යුගය"},
  {kanji:"暑", meaning:"උණුසුම්"},
  {kanji:"暗", meaning:"අඳුරු"},
  {kanji:"曲", meaning:"ගීතය /曲がる- වංගුව"},
  {kanji:"有", meaning:"ඇති"},
  {kanji:"服", meaning:"ඇඳුම"},
  {kanji:"期", meaning:"කාලය / පරිච්ඡේදය"},
  {kanji:"板", meaning:"පැනලය / තහඩු"},
  {kanji:"柱", meaning:"තීරුව"},
  {kanji:"根", meaning:"මූලය / අරු"},
  {kanji:"植", meaning:"වපුරනවා / තබනවා"},
  {kanji:"業", meaning:"ව්‍යාපාරය / කාර්යය"},
  {kanji:"様", meaning:"මනෝභාවය /様式 - ආකාරය"},
  {kanji:"横", meaning:"අඩ්දෙන් / පළල"},
  {kanji:"橋", meaning:"පොල්වතු"},
  {kanji:"次", meaning:"ඊළඟ"},
  {kanji:"歯", meaning:"දත්"},
  {kanji:"死", meaning:"මරණය"},
  {kanji:"殺", meaning:"මැරීම"},
  {kanji:"毒", meaning:"විෂය"},
  {kanji:"比", meaning:"සංසන්දනය"},
  {kanji:"毛", meaning:"රොම / හිසකෙස්"},
  {kanji:"氷", meaning:"අයිස් / හිම"},
  {kanji:"油", meaning:"තෙල්"},
  {kanji:"波", meaning:"තරංගය"},
  {kanji:"注", meaning:"注ぐ - දමනවා / 注文 - ඇණවුම"},
  {kanji:"洋", meaning:"බටහිර / සමුද්‍රය"},
  {kanji:"流", meaning:"ගලා යනවා"},
  {kanji:"消", meaning:"නැති කරනවා / 消す"},
  {kanji:"深", meaning:"ගැඹුරු"},
  {kanji:"温", meaning:"උණුසුම / 温度"},
  {kanji:"港", meaning:"වරාය"},
  {kanji:"湖", meaning:"කෙරළ / වැව"},
  {kanji:"湯", meaning:"ගرم වතුර"},
  {kanji:"漢", meaning:"චීන / කන්ජි"},
  {kanji:"炭", meaning:"ගගරොල් / කලුමල"},
  {kanji:"無", meaning:"නොමැති / හිස්"},
  {kanji:"然", meaning:"එහෙම / ස්වභාවය"},
  {kanji:"焼", meaning:"ගිනිගන්නවා / 烧く"},
  {kanji:"照", meaning:"ඇලවීම / ආලෝකය"},
  {kanji:"熱", meaning:"උණුසුම / උණ"},
  {kanji:"牧", meaning:"ගොවිතැන / කන්නය"},
  {kanji:"特", meaning:"විශේෂ"},
  {kanji:"産", meaning:"නිෂ්පාදනය / උපත"},
  {kanji:"由", meaning:"ඉතිහාසය / හේතුව"},
  {kanji:"申", meaning:"දැනුම් දෙනවා"},
  {kanji:"界", meaning:"ලෝකය / සීමාව"},
  {kanji:"畑", meaning:"වගා බිම"},
  {kanji:"病", meaning:"අසනීප"},
  {kanji:"登", meaning:"ඉහළ යනවා / 登る"},
  {kanji:"皮", meaning:"තොගය / කටාපත්"},
  {kanji:"皮", meaning:"තොගය / කටාපත්"},
  {kanji:"皿", meaning:"තැටිය / පිඟන්"},
  {kanji:"直", meaning:"නියම / සෘජු"},
  {kanji:"相", meaning:"අරමුණ / සම්බන්ධය"},
  {kanji:"省", meaning:"省く - අඩු කරනවා / අමාත්‍යාංශය"},
  {kanji:"看", meaning:"නරඹනවා / රැකබලා ගන්නවා"},
  {kanji:"真", meaning:"සත්‍ය / නියම"},
  {kanji:"研", meaning:"ගවේෂණය /研ぐ - සකසනවා"},
  {kanji:"究", meaning:"ගවේෂණය"},
  {kanji:"票", meaning:"ඡන්ද පත්‍රය / ටිකට්"},
  {kanji:"祭", meaning:"උත්සවය"},
  {kanji:"福", meaning:"සුභාශිංසන / සතුට"},
  {kanji:"科", meaning:"විෂය / විද්‍යාව"},
  {kanji:"秒", meaning:"තත්පරය"},
  {kanji:"究", meaning:"පර්යේෂණය"},
  {kanji:"章", meaning:"අධ්‍යාය / පද්‍යය"},
  {kanji:"童", meaning:"ළමයා / දරුවා"},
  {kanji:"第", meaning:"අංකය / පදනම"},
  {kanji:"笛", meaning:"පිඹුරුවාදනය"},
  {kanji:"等", meaning:"එවන් / වැනි"},
  {kanji:"箱", meaning:"ඇසුරුම / පෙට්ටිය"},
  {kanji:"類", meaning:"වර්ගය / කාණ්ඩය"},
  {kanji:"粉", meaning:"පිටි"},
  {kanji:"紀", meaning:"කාලය / සමාජය"},
  {kanji:"約", meaning:"ගිවිසුම / 約束 - පොරොන්දුව"},
  {kanji:"結", meaning:"ගැලපෙනවා /結ぶ - බැඳීම"},
  {kanji:"給", meaning:"පිරිවැය / දීම"},
  {kanji:"続", meaning:"දිගටම යන්න / つづく"},
  {kanji:"絵", meaning:"චිත්‍රය"},
  {kanji:"緑", meaning:"කොළ"},
  {kanji:"線", meaning:"රේඛාව"},
  {kanji:"練", meaning:"අභ්‍යාසය"},
  {kanji:"羊", meaning:"මියා / ගොනුව"},
  {kanji:"美", meaning:"ලස්සන"},
  {kanji:"習", meaning:"ඉගෙන ගන්නවා"},
  {kanji:"者", meaning:"පුද්ගලයෙක්"},
  {kanji:"育", meaning:"උපාසක / පෝෂණය"},
  {kanji:"苦", meaning:"වෙදනා / දුක්"},
  {kanji:"荷", meaning:"ගොනු / භාරය"},
  {kanji:"落", meaning:"පහළට වැටෙනවා"},
  {kanji:"葉", meaning:"කොළ / පත්‍රය"},
  {kanji:"薬", meaning:"ඖෂධය"},
  {kanji:"血", meaning:"ලේ"},
  {kanji:"表", meaning:"මුහුණ / 表す - දැක්වීම"},
  {kanji:"詩", meaning:"කවිය"},
  {kanji:"調", meaning:"調べる - පරීක්ෂාව / සංගීත සුර"},
  {kanji:"談", meaning:"සාකච්ඡාව"},
  {kanji:"議", meaning:"විවාදය / පාර්ලිමේන්තුව"},
  {kanji:"象", meaning:"අලි / සංකේතය"},
  {kanji:"貨", meaning:"මුදල් / සල්ලි"},
  {kanji:"貯", meaning:"ඉතිරි කරනවා / ආරක්ෂාව"},
  {kanji:"費", meaning:"වියදම"},
  {kanji:"賞", meaning:"සම්මානය"},
  {kanji:"資", meaning:"අරමුදල් / සම්පත්"},
  {kanji:"賛", meaning:"එකඟවීම / අනුමැතිය"},
  {kanji:"質", meaning:"ගුණාත්මක"},
  {kanji:"輸", meaning:"අපනයනය / ගෙන යනවා"},
  {kanji:"辺", meaning:"අසල / සීමාව"},
  {kanji:"達", meaning:"පහළ යනවා / 達する - ළඟා වීම"},
  {kanji:"選", meaning:"තෝරාගැනීම"},
  {kanji:"郡", meaning:"ග්‍රාමය / ප්‍රාදේශීය"},
  {kanji:"部", meaning:"කණ්ඩායම / අංශය"},
  {kanji:"都", meaning:"රාජධානිය"},
  {kanji:"配", meaning:"බෙදනවා"},
  {kanji:"酒", meaning:"මදිරාව"},
  {kanji:"録", meaning:"ලිපිගත කිරීම"},
  {kanji:"鉄", meaning:"ඉසුරු"},
  {kanji:"鏡", meaning:"කඳුළු / කැඳුළු"},
  {kanji:"陽", meaning:"ඇඳුම / සූර්යයා"},
  {kanji:"際", meaning:"අවස්ථාව / සන්ධිය"},
  {kanji:"雑", meaning:"විවිධ / අමුත්ත"},
  {kanji:"雲", meaning:"අමුතු / වලාකුළු"},
  {kanji:"順", meaning:"අනුපිළිවෙල / අනුගමනය"},
  {kanji:"願", meaning:"ඇල්ලීම / ඉල්ලීම"},
  {kanji:"類", meaning:"වර්ගය / සමූහය"},
  {kanji:"飛", meaning:"පියාසර කරනවා"},
  {kanji:"飯", meaning:"ඇත් බත"},
  {kanji:"養", meaning:"පෝෂණය / අධීන කිරීම"},
  {kanji:"験", meaning:"පරීක්ෂාව / අත්දැකීම"},
  {kanji:"警", meaning:"පොලීසිය / සටහන"},
  {kanji:"議", meaning:"කථිකාව / සභාව"},
  {kanji:"類", meaning:"ප්‍රභේදය"},
  {kanji:"競", meaning:"පොරොන්දුව / තරඟය"},
  {kanji:"養", meaning:"පෝෂණය / රැකබලාගැනීම"},
  {kanji:"難", meaning:"අමාරු / දුෂ්කර"},
  {kanji:"静", meaning:"නිවන් / සන්සුන්"},
  {kanji:"願", meaning:"කැමැත්ත / ප්‍රාර්ථනය"},
  {kanji:"類", meaning:"වර්ගය / සමූහය"},
  {kanji:"飛", meaning:"පියාසර කරනවා"},
  {kanji:"飯", meaning:"ඇත් බත් / කෑම"},
  {kanji:"養", meaning:"රැකගන්නවා / පෝෂණය"},
  {kanji:"験", meaning:"පරීක්ෂාව / අත්දැකීම"},
  {kanji:"警", meaning:"අවවාදය / පොලිස්"},
  {kanji:"議", meaning:"කතාබහ / සභාව"},
  {kanji:"際", meaning:"අවස්ථාව / සන්ධිය"},
  {kanji:"雑", meaning:"විවිධ / මිශ්‍ර"},
  {kanji:"非", meaning:"නො– / විරුද්ධ"},
  {kanji:"悲", meaning:"දුක / විලාපය"},
  {kanji:"想", meaning:"ඇඳුම් / සිතුවිලි"},
  {kanji:"感", meaning:"හදවතේ හැඟීම"},
  {kanji:"態", meaning:"අවස්ථාව / හැසිරීම"},
  {kanji:"慣", meaning:"පුරුදු"},
  {kanji:"技", meaning:"කෞශල්‍ය / තාක්ෂණය"},
  {kanji:"技", meaning:"විශේෂ කුසලතාව"},
  {kanji:"術", meaning:"කලා / විද්‍යාව / ක්‍රමය"},
  {kanji:"演", meaning:"නිෂ්පාදනය / ඉදිරිපත් කිරීම"},
  {kanji:"能", meaning:"ක්‍රියාකාරිත්වය / හැකියාව"},
  {kanji:"役", meaning:"භූමිකාව / සේවය"},
  {kanji:"務", meaning:"කාර්යය / යුතුකම"},
  {kanji:"功", meaning:"සාර්ථකත්වය"},
  {kanji:"労", meaning:"වැඩ / කම්කරුවන්"},
  {kanji:"加", meaning:"එකතු කරනවා"},
  {kanji:"協", meaning:"සහයෝගය"},
  {kanji:"参", meaning:"එක්වීම / පැමිණීම"},
  {kanji:"反", meaning:"එරෙහි / විරුද්ධ"},
  {kanji:"取", meaning:"ගන්නවා"},
  {kanji:"受", meaning:"ලැබීම / පිළිගැනීම"},
  {kanji:"告", meaning:"දැනුම් දීම"},
  {kanji:"周", meaning:"වටා / සම්පූර්ණ"},
  {kanji:"命", meaning:"ජීවිතය / නියෝගය"},
  {kanji:"和", meaning:"සමගිය / ජපන්"},
  {kanji:"固", meaning:"තද / ස්ථිර"},
  {kanji:"在", meaning:"පවතිනවා / තිබෙනවා"},
  {kanji:"報", meaning:"වාර්තාව"},
  {kanji:"境", meaning:"සීමාව / සන්ධිය"},
  {kanji:"増", meaning:"වැඩි කරනවා"},
  {kanji:"変", meaning:"වෙනස් කරනවා"},
  {kanji:"夢", meaning:"සපන / සිහින"},
  {kanji:"妻", meaning:"බිරිඳ"},
  {kanji:"婦", meaning:"ගෘහණිය"},
  {kanji:"容", meaning:"ඇතුළත් / හැඩය"},
  {kanji:"寄", meaning:"ඇමතුම / පරිත්‍යාගය"},
  {kanji:"寒", meaning:"සිසිල් / සීතල"},
  {kanji:"察", meaning:"පරීක්ෂාව / ගවේෂණය"},
  {kanji:"対", meaning:"විරුද්ධ / සම්බන්ධ"},
  {kanji:"局", meaning:"කාර්යාලය"},
  {kanji:"居", meaning:"පදිංචි"},
  {kanji:"島", meaning:"දූපත"},
  {kanji:"帳", meaning:"පොත / ලේඛනය"},
  {kanji:"平", meaning:"සම / සාමය"},
  {kanji:"幸", meaning:"ඉමහත් සතුට"},
  {kanji:"度", meaning:"අවස්ථාව / වාරය"},
  {kanji:"庫", meaning:"ගබඩාව"},
  {kanji:"庭", meaning:"වත්ත / අඟුළු වත්ත"},
  {kanji:"式", meaning:"ආකාරය / උත්සවය"},
  {kanji:"役", meaning:"කාර්යය / භූමිකාව"},
  {kanji:"彼", meaning:"ඔහු / යාලුවා"},
  {kanji:"往", meaning:"ගමන් / යාම"},
  {kanji:"必", meaning:"අනිවාර්ය / නිශ්චිත"},
  {kanji:"忘", meaning:"අමතක කරනවා"},
  {kanji:"念", meaning:"සිත්තම / කල්පනා"},
  {kanji:"愛", meaning:"ආදරය"},
  {kanji:"感", meaning:"හැඟීම / හැඟුම"},
  {kanji:"態", meaning:"තත්ත්වය / හැසිරීම"},
  {kanji:"慣", meaning:"පුරුදු වීම"},
  {kanji:"戦", meaning:"සටන / ගැටුම"},
  {kanji:"折", meaning:"වංගුව / කඩනවා"},
  {kanji:"投", meaning:"ගැසීම"},
  {kanji:"拾", meaning:"ගන්නවා / රැස් කරනවා"},
  {kanji:"指", meaning:"ඇඟිල්ල / යොමු කරනවා"},
  {kanji:"放", meaning:"නිදහස් කරනවා"},
  {kanji:"敗", meaning:"පරාජය"},
  {kanji:"散", meaning:"බෙදා හැරීම / විකිරණය"},
  {kanji:"求", meaning:"ඇල්ලීම / ඉල්ලීම"},
  {kanji:"治", meaning:"ඇරීම / සුව කරනවා"},
  {kanji:"法", meaning:"නීතිය"},
  {kanji:"済", meaning:"නිම කරනවා / සම්පූර්ණ"},
  {kanji:"参", meaning:"එක්වීම / සම්බන්ධ වීම"},
  {kanji:"受", meaning:"පිළිගැනීම / ලැබීම"},
  {kanji:"供", meaning:"පිරිනැමීම / සපයනවා"},
  {kanji:"価", meaning:"මිල / අගය"},
  {kanji:"限", meaning:"සීමාව"},
  {kanji:"況", meaning:"තත්ත්වය"},
  {kanji:"裁", meaning:"තීරණය /裁判 - නීතිය"},
  {kanji:"務", meaning:"කාර්යය / යුතුකම"},
  {kanji:"権", meaning:"අයිතිය / බලය"},
  {kanji:"設", meaning:"තැනීම / 設備 - පහසුකම"},
  {kanji:"軍", meaning:"යුධ හමුදාව"},
  {kanji:"団", meaning:"කණ්ඩායම / සංවිධානය"},
  {kanji:"県", meaning:"ප්‍රාන්තය"},
  {kanji:"兆", meaning:"ලක්ෂණය / සංඛ්‍යාව"},
  {kanji:"営", meaning:"ව්‍යාපාරය / කළමනාකරණය"},
  {kanji:"比", meaning:"සංසන්දනය"},
  {kanji:"構", meaning:"ව්‍යුහය / ගොඩනැගීම"},
  {kanji:"許", meaning:"අනුමැතිය"},
  {kanji:"証", meaning:"තහවුරු කිරීම / සාක්ෂි"},
  {kanji:"評", meaning:"විවේචනය / ඇගයීම"},
  {kanji:"課", meaning:"පන්තිය / මූලික කාර්යය"},
  {kanji:"講", meaning:"ඉගැන්වීම / දේශනය"},
  {kanji:"謝", meaning:"කණගාටු / ස්තූති"},
  {kanji:"資", meaning:"අරමුදල් / සම්පත්"},
  {kanji:"護", meaning:"ආරක්ෂාව / රැකවරණය"},
  {kanji:"豊", meaning:"පරිපූර්ණ / පොහොසත්"},
  {kanji:"輸", meaning:"ගෙනයාම / ගෙනියනවා"},
  {kanji:"述", meaning:"විස්තර කරනවා / ප්‍රකාශය"},
  {kanji:"迷", meaning:"මගුල් / ගැටළු"},
  {kanji:"夢", meaning:"සිහින"},
  {kanji:"達", meaning:"ලඟා වීම / පිරිස"},
  {kanji:"隊", meaning:"ඇල / බලකාය"},
  {kanji:"線", meaning:"රේඛාව"},
  {kanji:"階", meaning:"මහල / පියං"},
  {kanji:"陸", meaning:"බිම / දේශය"},
  {kanji:"静", meaning:"නිවන් / සන්සුන්"},
  {kanji:"順", meaning:"අනුපිළිවෙල"},
  {kanji:"願", meaning:"ඉල්ලීම / ප්‍රාර්ථනාව"},
  {kanji:"類", meaning:"වර්ගය / කාණ්ඩය"},
   // 👉 N3 එකට 363 Kanji
    ]
  };

  // ============ Script Logic ============
  let selectedLevel = null;
  let selectedData = [];
  let currentKanji = null;
  let score = 0, attempts = 0, asked = 0, totalQuestions = 0;

  const levelMenu = document.getElementById("levelMenu");
  const partMenu = document.getElementById("partMenu");
  const levelTitle = document.getElementById("levelTitle");
  const partsDiv = document.getElementById("parts");
  const quizArea = document.getElementById("quizArea");
  const kanjiBox = document.getElementById("kanjiBox");
  const optionsDiv = document.getElementById("options");
  const resultMsg = document.getElementById("resultMsg");
  const scoreBox = document.getElementById("scoreBox");
  const finalResultArea = document.getElementById("finalResultArea");
  const finalResult = document.getElementById("finalResult");

  showLevelMenu();

  function showLevelMenu() {
    levelMenu.style.display = "block";
    partMenu.style.display = "none";
    quizArea.style.display = "none";
    finalResultArea.style.display = "none";
  }

  function chooseLevel(level) {
    selectedLevel = level;
    levelTitle.textContent = `${level} Level - Kanji ${kanjiSets[level].length}ක් ඇත`;
    levelMenu.style.display = "none";
    partMenu.style.display = "block";
    partsDiv.innerHTML = "";

    const data = kanjiSets[level];
    let chunks = [];
    for (let i=0; i<data.length; i+=10) chunks.push(data.slice(i,i+10));

    chunks.forEach((p,i)=>{
      const cb=document.createElement("input");
      cb.type="checkbox"; cb.id="part"+i; cb.value=i;
      const lbl=document.createElement("label");
      lbl.htmlFor="part"+i;
      lbl.textContent=`Part ${i+1} (Kanji ${i*10+1} – ${i*10+p.length})`;
      partsDiv.appendChild(cb);
      partsDiv.appendChild(lbl);
      partsDiv.appendChild(document.createElement("br"));
    });
  }

  function startQuiz() {
    selectedData=[];
    const data=kanjiSets[selectedLevel];
    for(let i=0;i<data.length;i+=10){
      const cb=document.getElementById("part"+(i/10));
      if(cb && cb.checked) selectedData=selectedData.concat(data.slice(i,i+10));
    }
    if(selectedData.length===0){ alert("කරුණාකර කොටස් තෝරන්න!"); return; }

    const qSel=document.getElementById("questionCount").value;
    totalQuestions=(qSel==="all")?selectedData.length:parseInt(qSel);

    score=0;attempts=0;asked=0;updateScore();
    partMenu.style.display="none";quizArea.style.display="block";
    nextKanji();
  }

  function updateScore(){ scoreBox.textContent=`Score: ${score} / ${attempts}`; }

  function nextKanji(){
    if(asked>=totalQuestions){ showFinalResult(); return; }
    resultMsg.textContent=""; optionsDiv.innerHTML="";
    currentKanji=selectedData[Math.floor(Math.random()*selectedData.length)];
    kanjiBox.textContent=currentKanji.kanji;

    let options=[currentKanji.meaning];
    while(options.length<4){
      const wrong=selectedData[Math.floor(Math.random()*selectedData.length)].meaning;
      if(!options.includes(wrong)) options.push(wrong);
    }
    options.sort(()=>Math.random()-0.5);

    options.forEach(opt=>{
      const btn=document.createElement("button");
      btn.className="optionBtn"; btn.textContent=opt;
      btn.onclick=()=>checkAnswer(opt);
      optionsDiv.appendChild(btn);
    });
    asked++;
  }

  function checkAnswer(selected){
    attempts++;
    if(selected===currentKanji.meaning){
      score++; resultMsg.textContent="✅ හරි!"; resultMsg.style.color="green";
      updateScore(); setTimeout(nextKanji,1000);
    } else {
      resultMsg.textContent="❌ වැරදි"; resultMsg.style.color="red";
      updateScore();
    }
  }

  function showFinalResult(){
    quizArea.style.display="none"; finalResultArea.style.display="block";
    let percent=Math.round((score/attempts)*100);
    let grade=percent>=80?"A":percent>=60?"B":percent>=40?"C":"F";
    finalResult.innerHTML=`✅ අවසාන ප්‍රතිඵල:<br>ඔබගේ Score: ${score} / ${attempts}<br>Percentage: ${percent}%<br>Grade: ${grade}`;
  }
</script>

</body>
</html>
