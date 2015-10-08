# Bank-ATM-

import plotly.plotly as py #import library
from plotly.graph_objs import *
import plotly.tools as tls
tls.set_credentials_file(username='bemnat', api_key='qdjfx13q0e') #set username and key for library in order to use plotly library

class User:

	def __init__(self, pin): #attribute self and pin
		self.pin=pin
		self.balance=0
		self.transactionHistory=[]

	def checkPin(self, pin): #attributes self and pin
		return self.pin==pin

	def addTransaction(self, amount): #attributes self and amount
		self.transactionHistory.append(amount)    
		self.balance+=amount

#List of pre-existing users (first and last name) with their individual pin numbers.
###The user's transaction history is saved even if someone else uses the ATM in between
###So, as long as the program is not exited, the original user's balance can be retrieved back to them once they log in again.###
userNames = {'billy joe': User('5530'), 'mckenzie dully': User('8402'), 'bemnat lights': User('3847'), 'hannah road': User('0592'), 'josh smith': User('4751'), 'eric switcher': User('9428'), 'foller slickditch': User('2673')}

while True:
	#Welcomes the user
	print "##########################################"
	print "##                                      ##"
	print "##     Welcome to Tar Heel Banking!     ##"
	print "##                                      ##"
	print "##########################################"

	loggedIn=False
	while not loggedIn:
		print
		print "Please enter your name to login: ", #First thing program asks you when you run it
		userName = raw_input().rstrip().lower()
		print
		if not userName in userNames:	#if username entered does not exist in the pre-existing list
			print "User doesn't exist, try again"
		else:
			print "Enter your PIN: ",	
			pin = raw_input().rstrip()
			if userNames[userName].checkPin(pin): #Check each index of the userNames pre-existing list and see if pin number matches the corresponding username
				user = userNames[userName] 
				loggedIn=True		#If so, allow user to log in to his/her account
			else:							#if Pin number entered is incorrect
				print "Incorrect PIN, try again"	

	#after every transaction, it asks if the user would like to continue
	userContinue = "y"
	#this is the while loop to continue if the user said yes
	while userContinue.lower() == 'y':

		transactionChosen = False
		while not transactionChosen:
			#Ask what transaction the user would like to make
			print
			print "What transaction would you like to make?"
			print "1) Deposit"
			print "2) Withdraw"
			print "3) Print"
			print "Choice (1-3): ",
			userAnswer = raw_input().rstrip()

			if not userAnswer.isdigit(): #User can enter "deposit" OR "1" as a choice
				if userAnswer.lower()=="deposit":
					userAnswer=1
				elif userAnswer.lower()=="withdraw": #User can enter "withdraw" OR "2" as a choice
					userAnswer=2
				elif userAnswer.lower()=="print": #User can enter "print" OR "3" as a choice
					userAnswer=3
				else:
					userAnswer=4
			else:
				userAnswer=int(userAnswer)

			if userAnswer>0 and userAnswer<4: #Choices MUST be 1, 2, or 3. Numbers 0 and 4 are NOT included as userAnswer
				transactionChosen=True
			else:
				print "Invalid transaction type, try again" #If user types anything else other than 1 Deposit, 2 Withdraw or 3 Print

		#if the user wants to make a deposit or a withdrawal, asks how much they would like to deposit or withdraw
		if  userAnswer==1 or userAnswer==2:
			types = ["Deposit", "Withdraw"]
			print "%s amount: "%types[userAnswer-1],
			transaction= float(raw_input().rstrip())
			if userAnswer == 2:
				transaction*=-1
			user.addTransaction(transaction)	#Stores transactions (withdrawal or deposit) for later


		#if after this particular transaction the user's account hits the 
		#negatives, the program will charge a $20 penalty
			if user.balance < 0:
				print "Your current balance has reached negative!"
				print "A penalty of $20 will be applied to your account."
				user.balance -= 20

		#if the user wants to print a receipt:
		elif userAnswer == 3:	#receipt is printed if user choosed 3 (or types "Print")
			print "Below are your latest recent transactions (up to 3 shown below):"
			for i in user.transactionHistory[-3:]:	#Starts to print out starting with the most recent transaction done (if all 3 transactions done, prints 3rd one first)
				if i>=0:
					print "Deposit $%.2f"%i
				else:
					print "Withdrawl $%.2f"%(i*-1)

			#Starting states
			numDeposits=0  #number of deposit made
			numWithdrawls=0 #number of withdrawals made
			depositSum=0 #sum of deposits made
			withdrawlSum=0 #sum of withdrawals made
			for i in user.transactionHistory:
				if i>=0: #if transaction is greater than zero
					numDeposits+=1 #Add one to numDeposits (number of transactions made)
					depositSum+=i  #Store all the deposits made to variable depositSum
				else:
					numWithdrawls+=1 #Add one to numWithdrawals (number of withdrawals made)
					withdrawlSum+=i*-1 #Store all the withdrawals made to variable withdrawlSum

			if numDeposits>0:
				realDeposit=depositSum/numDeposits	#Find the average deposit transactions made
			else:
				realDeposit=0 #If no deposits at all made then no average is found at all

			if numWithdrawls>0:
				realWithdrawl=withdrawlSum/numWithdrawls	#Find the average withdrawal made
			else:
				realWithdrawl=0  #If no withdrawals made at all then no average is found at all
			print "Current Balance: $%.2f"%user.balance  #Prints the user's current balance
			print "Average Deposit Amount: $%.2f"%realDeposit	#Prints the average deposits made
			print "Average Withdraw Amount: $%.2f"%realWithdrawl   #Prints the average withdrawals made

			balances=[]		#Creates an array
			partialSum=0
			for i in user.transactionHistory:
				partialSum+=i
				balances.append(partialSum) #Adds sums to array of balances


			#Create a scatter/line graph for transaction history from plotly library

			###If slope is positive, deposit made. If slope is negative, withdrawal made. Line shows the change in account balance after each transaction###

			data = Data([ #Create all the attributes wanted
				Scatter(	#Make a scatter/line Plot
		    		x=range(1,len(balances)+1),		#Create up to 3 X-values for each transaction made
		    		y=balances,		#Creates Y-values showing actual account $ balance after each transaction
		    		marker=Marker(
		    			color = ['#9467bd', '#db5a44', '#ff7f0e'] #Create colors for each dot of the line graph
		    		)
				)
			])
			layout = Layout(
				plot_bgcolor='#EFECEA',	#Change background of bar graph to light gray
	    		title='Your Transaction History', #Add a title for the bar graph
				titlefont=Font(		
	    			size = 25,		#Change title to a large font size
	    			color= 'rgba(0, 0, 0, 200)'		#Make the title black in color
	    		),
	    		xaxis=XAxis(
	    			title='Number of Transactions', #Add title for X-axis
	    			titlefont=Font(
	    				size = 15, 		#Font for X-axis title
	    				color= 'rgb(0, 100, 200)'		#Color for X-axis title. No red. Mixture of light green and light blue.
	 				),
	 				tickfont=Font(
	            	size=14,			#Font for X-axis labels
	            	color='rgb(0, 0, 100)'	#Color for X-axis labels. Just dark blue.
	            	)
	    		),
	    		yaxis=YAxis(
	        		title='Changes in the Account Balance ($)',	#Add title for Y-axis
	        		titlefont=Font(
	    				size = 15,		#Font for Y-axis title
	    				color='rgb(0, 175, 0)'		#Color for Y-axis title. No red. No blue. Light green.
	    			),
	    			tickfont=Font(
	            	size=14,			#Font for Y-axis labels
	            	color='rgb(0, 100, 0)'	#Color for Y-axis labels. Just dark green.
	            	)
	        	)
	    	)
			plot_url = py.plot(Figure(data=data, layout=layout), filename='bankATM3')	#Implement plot by putting "data" and "layout" attributes in object "Figure"
			#And putting "Figure" inside the plot library function to make the plot for this file


		#if the user said anything else other than deposit, withdraw, print	
		else:
			print "Unrecognized Option"

		#asks the user if they would like to continue the program
		print "Would you like to make another transaction? (y/n) ",
		userContinue = raw_input().rstrip()

	#when the program asks if the user wants to continue and the user says anything other than "y" (yes) or "n" (no)
	if userContinue.lower() != "n":
		print "Invalid response"
	#when the program asks if the user wants to coninue and the user says "n" (no)
	print "Thank you for using Tar Heel Banking!"
	print "Goodbye."
	print
	#Like a real atm machine, it always keeps running.
	#It will go back to the while loop and ask for a log in if user says no or anything else, besides yes
	###The user's transaction history is saved even if someone else uses the ATM in between
	#######So, as long as the program is not exited, the original user's balance can be retrieved back to them once they log in again.#######

