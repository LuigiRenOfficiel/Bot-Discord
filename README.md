import discord
import tweepy

# Configurer les clés d'API Twitter
consumer_key = "VOTRE_CONSUMER_KEY"
consumer_secret = "VOTRE_CONSUMER_SECRET"
access_token = "VOTRE_ACCESS_TOKEN"
access_token_secret = "VOTRE_ACCESS_TOKEN_SECRET"

# Configurer les clés d'API Discord
discord_token = "VOTRE_TOKEN_DISCORD"

# Configurer l'ID du canal Discord où le bot enverra les tweets
channel_id = "ID_DU_CANAL"

# Authentification Twitter
auth = tweepy.OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
twitter_api = tweepy.API(auth)

# Créer un client Discord
client = discord.Client()

@client.event
async def on_ready():
    print(f"Connecté en tant que {client.user.name}")

@client.event
async def on_message(message):
    if message.content.startswith("!tweet"):
        # Obtenir le texte du tweet à partir de la commande
        tweet_text = message.content[7:]
        
        try:
            # Poster le tweet
            tweet = twitter_api.update_status(tweet_text)
            tweet_url = f"https://twitter.com/{tweet.user.screen_name}/status/{tweet.id_str}"
            
            # Envoyer le lien du tweet sur Discord
            channel = client.get_channel(channel_id)
            await channel.send(tweet_url)
        except tweepy.TweepError as e:
            print(e)
            await message.channel.send("Une erreur s'est produite lors de l'envoi du tweet.")

# Lancer le bot Discord
client.run(discord_token)
