---
title: "Blackjack "
date: "2018-10-23"
tags: [Blackjack Game]
header:
  images: "images/single-degree/blackjack.png"
---

This is a blackjack game that i created using a combination of Python functions
for the back-end, and TkInter for the front-end.

Python code block:
```python
import tkinter
import random

# CREATING FUNCTION TO LOAD THE IMAGES/ CARDS


def load_cards(card_images):
    suits = ['club', 'diamond', 'heart', 'spade']
    face_cards = ['jack', 'king', 'queen']

    if tkinter.TkVersion >= 8.6:
        extension = 'png'
    else:
        extension = 'ppm'

    # for each suit retrieve image from card

    for suit in suits:
        for card in range(1, 11):
            name = "cards/{}_{}.{}".format(str(card), suit, extension)  # Creating the path for the images
            image = tkinter.PhotoImage(file=name)
            card_images.append((card, image, suit,))

        # create a loop for the face cards

        for face in face_cards:
            name = 'cards/{}_{}.{}'.format(face, suit, extension)
            image = tkinter.PhotoImage(file=name)
            card_images.append((10, image,))


def _deal_card(frame):                # Function to deal cards whenever the dealer or player button is pressed

    # pop the next card off the top of the deck
    next_card = deck.pop(0)

    # add the image to a label and display the label
    tkinter.Label(frame, image=next_card[1], relief='raised').pack(side='left')

    return next_card


def score_hand(hand):
    # This function tallies up the score of a hand/ list that is passed
    # only one ace can have the value of 11, and the value of the ace will be reduced to 1 if the hand goes bust.
    score = 0
    ace = False      # creating two local variables, score and ace
    for next_card in hand:
        card_value = next_card[0]
        if card_value == 1 and not ace:
            ace = True
            card_value = 11
        score += card_value
        # if we go bust then reduce value of ace back to 1
        if score > 21 and ace:
            score -= 10
            ace = False
    return score


# create two functions that run the _deal_card function with a specified parameter,
# so we can link it to the buttons using the command=


def deal_dealer():
    dealer_score = score_hand(dealer_hand)
    while 0 < dealer_score < 17:
        dealer_hand.append(_deal_card(dealer_card_frame))   # dealer will auto grab another card if his score is less 17
        dealer_score = score_hand(dealer_hand)
        dealer_score_label.set(dealer_score)

    # We need to compare dealer hand to player hand
    player_score = score_hand(player_hand)
    if player_score > 21:
        result_text.set("The Dealer Wins, You LOSE!!")
    elif 21 >= player_score > dealer_score or dealer_score > 21:
        result_text.set('The Player wins')
    elif dealer_score > player_score:
        result_text.set("The Dealer Wins, You LOSE!!")
    else:
        result_text.set('Draw')


def _deal_player():
    player_hand.append(_deal_card(player_card_frame))
    player_score = score_hand(player_hand)

    player_score_label.set(player_score)
    if player_score > 21:
        result_text.set("The Dealer Wins, You LOSE!!")


def reset_hand(hand):
    hand.clear()


def initial_deal():   

    _deal_player()
    dealer_hand.append(_deal_card(dealer_card_frame))
    dealer_score_label.set(score_hand(dealer_hand))
    _deal_player()



def new_game():
    # destroy player, and dealer card frame, and create new ones
    global player_card_frame
    global dealer_card_frame
    player_card_frame.destroy()
    player_card_frame = tkinter.Frame(card_frame, background='green', borderwidth=1, relief='sunken')
    player_card_frame.grid(row=2, column=1, sticky='ew', rowspan=2)

    dealer_card_frame.destroy()
    dealer_card_frame = tkinter.Frame(card_frame, relief='sunken', borderwidth=1, background='green')
    dealer_card_frame.grid(row=0, column=1, sticky='ew', rowspan=2)

    result_text.set('')

    # Reset player hand and dealer hand
    reset_hand(player_hand)
    reset_hand(dealer_hand)

    # Score new player hand and dealer hand

    initial_deal()


def shuffle():
    random.shuffle(deck)


# Function to officially start the new game
def play():
    initial_deal()

    mainWindow.mainloop()


mainWindow = tkinter.Tk()

# Set up the screen and frames for the dealer and player

mainWindow.title('21/Black Jack Game')
mainWindow.geometry('640x480')
mainWindow.config(background='purple')

# Creating the string variable, and the results label running across the top of the screen

result_text = tkinter.StringVar()
result_Label = tkinter.Label(mainWindow, textvariable=result_text, relief='sunken')
result_Label.grid(row=0, column=0, columnspan=3, sticky='ne')

# Creating the frame to house the cards

card_frame = tkinter.Frame(mainWindow, relief='sunken', borderwidth=1, background='green')
card_frame.grid(row=1, column=0, sticky='ew', columnspan=3, rowspan=2)

# Creating the dealer score label

dealer_score_label = tkinter.IntVar()
tkinter.Label(card_frame, text='Dealer', background='green', fg='white').grid(row=0, column=0)
tkinter.Label(card_frame, textvariable=dealer_score_label, background='green', fg='white').grid(row=1, column=0)

# Embedded frame to hold card images for dealer

dealer_card_frame = tkinter.Frame(card_frame, relief='sunken', borderwidth=1, background='green')
dealer_card_frame.grid(row=0, column=1, sticky='ew', rowspan=2)

# Creating player score label

player_score_label = tkinter.IntVar()
# Initialising two variables, one to store player score, and one to store no of aces

tkinter.Label(card_frame, text='Player', background='green', fg='white').grid(row=2, column=0)
tkinter.Label(card_frame, textvariable=player_score_label, background='green', fg='white').grid(row=3, column=0,
                                                                                                sticky='ew')

# Embedded frame to hold card images for player

player_card_frame = tkinter.Frame(card_frame, background='green', borderwidth=1, relief='sunken')
player_card_frame.grid(row=2, column=1, sticky='ew', rowspan=2)

# Adding the dealer, and player buttons, and the button frame

button_frame = tkinter.Frame(mainWindow, relief='sunken', borderwidth=1, background='black')
button_frame.grid(row=3, column=0, columnspan=3, sticky='w')

dealer_button = tkinter.Button(button_frame, text='Dealer', command=deal_dealer)
dealer_button.grid(row=0, column=0)


player_button = tkinter.Button(button_frame, text='Player', command=_deal_player)
player_button.grid(row=0, column=1)

# CREATING A NEW GAME BUTTON

new_game_button = tkinter.Button(button_frame, text='New Game', command=new_game)
new_game_button.grid(row=0, column=2)

# Add shuffle button

shuffle_button = tkinter.Button(button_frame, text='Shuffle', command=shuffle)
shuffle_button.grid(row=0, column=3)

# load cards

cards = []
load_cards(cards)
print(cards)

# Creating a new deck for cards and shuffling them

deck = list(cards) + list(cards)  

shuffle()
print(__name__)

# Create the list to store the dealers, and player's hands

dealer_hand = []
player_hand = []

# You can quickly use the cmd click function to determine if a variable is local or global

if __name__ == '__main__':   # this condition checks the name of the name attribute for the module = __main__
    play()
```
