# Chatbot-
{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [],
   "source": [
    "#Proprietary content. © Great Learning. All Rights Reserved. Unauthorized use or distribution prohibited."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "9Zj9h92wUDPN"
   },
   "source": [
    "**Importing the required libraries**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "4BmyocFbb0MJ"
   },
   "outputs": [],
   "source": [
    "import numpy as np\n",
    "import nltk\n",
    "import string\n",
    "import random"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "85QE5FDSUKqU"
   },
   "source": [
    "**Importing and reading the corpus**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "jouIkYEkb9Pk"
   },
   "outputs": [],
   "source": [
    "f=open('chatbot.txt','r',errors = 'ignore')\n",
    "raw_doc=f.read()\n",
    "raw_doc=raw_doc.lower() #Converts text to lowercase\n",
    "nltk.download('punkt') #Using the Punkt tokenizer\n",
    "nltk.download('wordnet') #Using the WordNet dictionary\n",
    "sent_tokens = nltk.sent_tokenize(raw_doc) #Converts doc to list of sentences \n",
    "word_tokens = nltk.word_tokenize(raw_doc) #Converts doc to list of words"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "pmXgGkVeUSUb"
   },
   "source": [
    "**Example of sentance tokens**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "Swu4WRVncPR8"
   },
   "outputs": [],
   "source": [
    "sent_tokens[:2]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "Gtkzd0KhUWJT"
   },
   "source": [
    "**Example of word tokens**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "hcwrvmWicaLc"
   },
   "outputs": [],
   "source": [
    "word_tokens[:2]"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "bvvYcZZ9UbVD"
   },
   "source": [
    "**Text preprocessing**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "YbZllVqBcc78"
   },
   "outputs": [],
   "source": [
    "lemmer = nltk.stem.WordNetLemmatizer()\n",
    "#WordNet is a semantically-oriented dictionary of English included in NLTK.\n",
    "def LemTokens(tokens):\n",
    "    return [lemmer.lemmatize(token) for token in tokens]\n",
    "remove_punct_dict = dict((ord(punct), None) for punct in string.punctuation)\n",
    "def LemNormalize(text):\n",
    "    return LemTokens(nltk.word_tokenize(text.lower().translate(remove_punct_dict)))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "tLX8WBE4UgOr"
   },
   "source": [
    "**Defining the greeting function**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "dLOqphibchJM"
   },
   "outputs": [],
   "source": [
    "GREET_INPUTS = (\"hello\", \"hi\", \"greetings\", \"sup\", \"what's up\",\"hey\")\n",
    "GREET_RESPONSES = [\"hi\", \"hey\", \"*nods*\", \"hi there\", \"hello\", \"I am glad! You are talking to me\"]\n",
    "def greet(sentence):\n",
    " \n",
    "    for word in sentence.split():\n",
    "        if word.lower() in GREET_INPUTS:\n",
    "            return random.choice(GREET_RESPONSES)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "qJhFmyRCUm4j"
   },
   "source": [
    "**Response generation**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "eo7Kv52HcjG0"
   },
   "outputs": [],
   "source": [
    "from sklearn.feature_extraction.text import TfidfVectorizer\n",
    "from sklearn.metrics.pairwise import cosine_similarity"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "JEHZesw3cnNM"
   },
   "outputs": [],
   "source": [
    "def response(user_response):\n",
    "  robo1_response=''\n",
    "  TfidfVec = TfidfVectorizer(tokenizer=LemNormalize, stop_words='english')\n",
    "  tfidf = TfidfVec.fit_transform(sent_tokens)\n",
    "  vals = cosine_similarity(tfidf[-1], tfidf)\n",
    "  idx=vals.argsort()[0][-2]\n",
    "  flat = vals.flatten()\n",
    "  flat.sort()\n",
    "  req_tfidf = flat[-2]\n",
    "  if(req_tfidf==0):\n",
    "    robo1_response=robo1_response+\"I am sorry! I don't understand you\"\n",
    "    return robo1_response\n",
    "  else:\n",
    "    robo1_response = robo1_response+sent_tokens[idx]\n",
    "    return robo1_response"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "1Q-iY_o1Utas"
   },
   "source": [
    "**Defining conversation start/end protocols**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "id": "wxzENVDgdNGd"
   },
   "outputs": [],
   "source": [
    "flag=True\n",
    "print(\"BOT: My name is Stark. Let's have a conversation! Also, if you want to exit any time, just type Bye!\")\n",
    "while(flag==True):\n",
    "    user_response = input()\n",
    "    user_response=user_response.lower()\n",
    "    if(user_response!='bye'):\n",
    "        if(user_response=='thanks' or user_response=='thank you' ):\n",
    "            flag=False\n",
    "            print(\"BOT: You are welcome..\")\n",
    "        else:\n",
    "            if(greet(user_response)!=None):\n",
    "                print(\"BOT: \"+greet(user_response))\n",
    "            else:\n",
    "                sent_tokens.append(user_response)\n",
    "                word_tokens=word_tokens+nltk.word_tokenize(user_response)\n",
    "                final_words=list(set(word_tokens))\n",
    "                print(\"BOT: \",end=\"\")\n",
    "                print(response(user_response))\n",
    "                sent_tokens.remove(user_response)\n",
    "    else:\n",
    "        flag=False\n",
    "        print(\"BOT: Goodbye! Take care <3 \")"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {
    "id": "f5yXpRQMXopU"
   },
   "source": [
    "\n",
    "\n",
    "---\n",
    "\n"
   ]
  }
 ],
 "metadata": {
  "colab": {
   "collapsed_sections": [],
   "name": "Building a chatbot in Python",
   "provenance": []
  },
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 1
}
