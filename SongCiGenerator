import g4f

def readCipai(cipai):
    fName = cipai + ".txt"
    f = open(fName, "r")
    cipaiFormat = []
    line = f.readline()
    while(line):
        line = f.readline()
	   sentences = line.split("。")
        for sentence in sentences:
            cipaiFormat.append(sentence)
    return cipaiFormat

def readRhymeTable():
    fName = "韵表.txt"
    f = open(fName, "r")
    rhymeDict = {"X": "Unknown"}
    pingzeDict = {"X": "Unknown"}
    currRhyme = "Unknown"
    currPingze = "Unknown"
    line = f.readline().strip()
    while(line):
        if line == "Ping":
            # Start of Ping characters
            currPingze = "Ping"
        elif line == "Shang":
            # Start of Shang characters
            currPingze = "Shang"
        elif line == "Qu":
            # Start of Ping characters
            currPingze = "Qu"
        elif len(line) >=4:
            currRhyme = line
        else:
            rhymeDict[line] = currRhyme
            pingzeDict[line] = currPingze
        line = f.readline().strip()
    return (rhymeDict, pingzeDict)

def processAPoem(rhymeDict, sentenceDict, keyword):
    response = g4f.ChatCompletion.create(model="gpt-3.5-turbo", provider=g4f.Provider.You, message=[{"role": "user", "content": "写一首关于" + keyword + "的宋词"}])
    print(response)
    sentences = response.split("。")
    for sentence in sentences:
        if len(sentence) > 30:
            continue
        trimmedSentence = sentence.strip()
        shortSentences = trimmedSentence.split("，")
        for shortSentence in shortSentences:
            trimmedShortSentence = shortSentence.strip()
            if "了" in trimmedShortSentence or "的" in trimmedShortSentence or "着" in trimmedShortSentence or "啊" in trimmedShortSentence:
                continue
            rhymeChar = trimmedShortSentence[len(trimmedShortSentence) - 1:]
            if rhymeChar in rhymeDict:
                rhyme = rhymeDict[rhymeChar]
            else:
                continue
            sentenceLen = len(trimmedShortSentence)
            if sentenceLen > 9:
                continue
            if sentenceLen in sentenceDict:
                if rhyme in sentenceDict[sentenceLen]:
                    sentenceDict[sentenceLen][rhyme].append(trimmedShortSentence)
                else:
                    sentenceDict[sentenceLen][rhyme] = [trimmedShortSentence]
            else:
                sentenceDict[sentenceLen] = {rhyme: [trimmedShortSentence]}
    return sentenceDict

def decideARhyme(sentenceDict, cipaiFormat):
# Check sentenceDict
# Also check cipai
    rhyme = "Unknown"
    rhymeSentences = {2:0, 3:0, 4:0, 5:0, 6:0, 7:0}
    for sentence in cipaiFormat:
        trimmedSentence = sentence.strip()
        shortSentences = trimmedSentence.split("，")
        for index in range(len(shortSentences)):
            shortSentence = shortSentences[index]
            trimmedShortSentence = shortSentence.strip()
            sentenceLen = len(trimmedShortSentence)
            if index == len(shortSentences):
                rhymeSentences[sentenceLen] = rhymeSentences[sentenceLen] + 1
    optionRhymes = []
    for length in rhymeSentences.keys():
        sentenceNumber = rhymeSentences[length]
        if (sentenceNumber == 0):
           continue
        availableRhymes = []
        for optionRhyme in sentenceDict[sentenceLen]:
            if len(sentenceDict[sentenceLen][optionRhyme]) > sentenceNumber:
                availableRhymes.append(optionRhyme)
        optionRhymes.append(availableRhymes)
    for selectedRhyme in optionRhymes[0]:
        passRule = True
        for line in optionRhymes:
            satisfied = False
            for optionRhyme in line:
                if selectedRhyme == optionRhyme:
                    satisfied = True
            if (satisfied == False):
                passRule = False
        if (passRule == True):
            return selectedRhyme
    return rhyme

def generatePoem(sentenceDict, rhymeDict, pingzeDict, cipaiFormat):
# Decide a rhyme
# Follow the cipaiFormat, replace each short sentence
## If the sentence doesn't end with ".", randomly find a sentence in sentence library
## Otherwise find a sentence that has the same rhyme as our decided rhyme.
# Print the poem out
    sentences = []
    for formatLine in cipaiFormat:
        formatSentences = formatLine.split("。")
        for formatSentence in formatSentences:
            if len(formatSentence) > 1:
                sentences.append(formatSentence)
    rhyme = decideARhyme(sentenceDict)
    sentenceUsed = []
    generatedPoem = ""
    for sentence in sentences:
        trimmedSentence = sentence.strip()
        shortSentences = trimmedSentence.split("，")
        for index in range(len(shortSentences)):
            shortSentence = shortSentences[index]
            trimmedShortSentence = shortSentence.strip()
            sentenceLen = len(trimmedShortSentence)
            if index == len(shortSentences) - 1:
                optionSentences = sentenceDict[sentenceLen][rhyme]
                for optionSentence in optionSentences:
                    if optionSentence not in sentenceUsed:
                        generatedPoem += optionSentence
                        sentenceUsed.append(optionSentence)
                generatedPoem += "。"
            else:
                selectedRhyme = "Unknown"
                while(True):
                    randomIndex = random.randint(0, len(sentenceDict[sentenceLen]) - 1)
                    optionRhyme = list(sentenceDict[sentenceLen].keys())[randomIndex]
                    if optionRhyme != rhyme and len(sentenceDict[sentenceLen][optionRhyme]) > 0:
                        selectedRhyme = optionRhyme
                        break
                optionSentences = sentenceDict[sentenceLen][optionRhyme]
                for optionSentence in optionSentences:
                    if optionSentence not in sentenceUsed:
                        generatedPoem += optionSentence
                        sentenceUsed.append(optionSentence)
                generatedPoem += "，"
    return generatedPoem 



cipaiFormat = readCipai("青玉案")
（rhymeDict, pingzeDict）= readRhymeTable()
sentenceDict = {1: {"Unknown":["X"]}}
keyword = input("请输入个诗词的主题")
for query in range(2):
    sentenceDict = processAPoem(rhymeDict, sentenceDict, keyword)
generatedPoem = generatePoem(sentenceDict, rhymeDict, pingzeDict, cipaiFormat)
print(generatedPoem)



