<img src="media/AUEB_logo.jpg" width="425" /> <img src="media/BA_Lab.png" width="425" />
# Προγραμματισμός ΙΙ
# Νήματα

* [Στέφανος Γεωργίου](https://www.balab.aueb.gr/stefanos-georgiou.html)
* [Κωνσταντίνος Κραββαρίτης](https://www.balab.aueb.gr/konstantinos-kravvaritis.html)


# Νήματα (Threads)

* Νήματα είναι κομμάτια κώδικα που "ζούνε" μέσα σε μιά διεργασία.
* Διεργασία είναι ένα πρόγραμμα που εκτελείτε είτε θα εκτελεστεί.
* Όταν δημιουργείτε μια διεργασία δεσμεύετε κάποιος χώρος μνήμης.
* Μια δεριγασία αποτελείται από τουλάχιστον ένα νήμα.
* Χρησιμοποιούνται για συγχονισμό και παραλληλοποιήση. 


# Διεργασίες Παράδειγμα

![](media/Processes_SnapShot.png)


# Νήματα Παράδειγμα

![](media/Threads_Snapshot.png)


# Πρόβλημα Συγχρονισμού (1)

* Ένα ζευγάρι έχει ένα κοινό λογαρισμό
* Ταυτόχρονα προσθέτουν χρήματα από διαφορετικά ATMς

![](media/Concurrency_Bank_Accounts_Example.png)


# Πρόβλημα Συγχονισμού (2)

* Σενάριο με κανονική ροή
```java
get balance (balance = $50)
add $100
write back result (balance = $150)
get balance (balance = $150)
add $50 
write back result (balance = $200)	
```

* Σενάριο με πρόβλημα συγχρονισμού
```java
get balance (balance = $50)
get balance (balance = $50)
add $100
add $50
write back result (balance = $150)
write back result (balance = $100)
```


# Παράδειγμα σε κώδικα (1)
```java
public class SynchronousProblem {
	private static int balance = 50;
	
	public int getBalance() {
		return this.balance;
	}	

	public void setBalance(int balance) {
		this.balance = balance;
	}
}
```


# Παράδειγμα σε κώδικα (2)
```java
public class TestSynch {

	public static void main(String[] args) {
		SynchronousProblem commonAccount = new SynchronousProblem();
		int wifesBalance = commonAccount.getBalance();
		int husbandsBalance = commonAccount.getBalance(); 
		commonAccount.setBalance(wifesBalance += 100);
		commonAccount.setBalance(husbandsBalance += 50);
		System.out.println("The current balance is $" 
		+ commonAccount.getBalance());
	}
}

```


# Προσοχή!
![](media/if_something_can_go_wrong.jpg)



# Πρόβλημα Παραλληλισμού (1)

* Σενάριο με παραλληλισμό
```java
get balance(balance=$50)	get balance(balance=$50)
add $100			add $50
write result(balance=$150) 	write results(balace=$100)
```


# Εκτέλεση νημάτων (1)

* Η Java υποστηρίζει δύο διαφορετικούς τρόπους για εκτέλεση νημάτων
* Δήλωση κλάσης που να επεκτήνει την Thread
* Υπερσκελίζει (Override) την run της κλάσης Thread


# Εκτελεση νημάτων (2)

```java
class FindCountOfEvenNumbers extends Thread { 
      private int maxNumber;
      private int count;
      FindCountOfEvenNumbers(int maxNumber) {
          this.maxNumber = maxNumber;
          this.count = 0;        
      }
      @Override	
      public void run() {
          for (int i=0; i<this.maxNumber; ++i)
          {
              if (i % 2 == 0)
                  ++count;        
          }    
      }
  }
```


# Εκτέλεση νημάτων

```java 
public class TestThreads {

	public static void main(String[] args) {

		// Ένας τρόπος
		FindCountOfEvenNumbers findNumber = new FindCountOfEvenNumbers(1200);
		findNumber.start();
		
		// Άλλος τρόπος
		Thread t = new Thread(findNumber);
		t.start();
		
		System.out.println(findNumber.count);
	}
}
```


# Εκτέλεση νημάτων 

* Δήλωση κλάσης που να υλοποιεί την διεπαφή Runnable 
* Αυτή η κλάση υλοποιεί την μέθοδο run της Runnable.

```java
class FindCountOfEvenNumbers implements Runnable {
      private int maxNumber;
      private int count;
      FindCountOfEvenNumbers(int maxNumber) {
          this.maxNumber = maxNumber;
          this.count = 0;        
      }

      public void run() {
          for (int i=0; i<this.maxNumber; ++i)
          {
              if (i % 2 == 0)
                  ++count;        
          }    
      }
  }
```


# Διαφορές μεταξύ Thread και Runnable

* Αφού επεκτείνουμε την κλάση Thread δεν μπορούμε στο μέλλον να επεκτείνουμε άλλη κλάση.
* Αν υλοποιήσουμε την Runnable μπορούμε στην συνέχεια να επεκτείνουμε κάποια άλλη κλάση.



# Παράδειγμα Thread Vs Runnable (1)

```java
//Implementing Runnable Interface
    class ImplementsRunnable implements Runnable {
        private int counter = 0;
        public void run() {
            counter++;
            System.out.println("ImplementsRunnable : Counter : "+ counter);
        }
    }

    //Extending Thread class
    class ExtendsThread extends Thread {
        private int counter = 0;
		@Override
        public void run () {
            counter++;
            System.out.println("ExtendsThread : Counter : "+ counter);
        }
    }
``` 


# Παράδειγμα Thread Vs Runnable (2)

```java
public class ThreadVsRunnable {
        public static void main(String args[]) throws Exception {
                // Multiple threads share the same object.
                ImplementsRunnable rc = new ImplementsRunnable();
                Thread t1 = new Thread(rc);
                t1.start();
                Thread.sleep(1000); 
                Thread t2 = new Thread(rc);
                t2.start();
                Thread.sleep(1000); 
                Thread t3 = new Thread(rc);
                t3.start();

                ExtendsThread tc1 = new ExtendsThread();
                tc1.start();
                Thread.sleep(1000);
                ExtendsThread tc2 = new ExtendsThread();
                tc2.start();
                Thread.sleep(1000); 
                ExtendsThread tc3 = new ExtendsThread();
                tc3.start();
         }
    }
```


# Αποτέλεσμα εκλεσης  

```java
    ImplementsRunnable : Counter : 1
    ImplementsRunnable : Counter : 2
    ImplementsRunnable : Counter : 3

    ExtendsThread : Counter : 1
    ExtendsThread : Counter : 1
    ExtendsThread : Counter : 1
```


# Thread join(1)

* Μέθοδος που επιτρέπει σε ένα νήμα να περιμένει μέχρι να ολοκληρωθεί η εκτέλεση ένος άλλου.

```java
  Thread t = new Thread(findNumber);
  t.start();
  System.out.println("Now thread " + t.getName() + " is running");
  t.join();
```


# Παράδειγμα με νήματα

```java
public class SimpleThreads {
            static void threadMessage(String message) {
                String threadName = Thread.currentThread().getName();
                System.out.format("%s: %s%n",threadName, message);
        }

            private static class MessageLoop implements Runnable {
            public void run() {
                String importantInfo[] = {"Mares eat oats","Does eat oats","Little lambs eat ivy","A kid will eat ivy too"};
                try {
                for (int i = 0; i < importantInfo.length; i++) {
                    Thread.sleep(4000);
                    threadMessage(importantInfo[i]);
                }
                } catch (InterruptedException e) {threadMessage("I wasn't done!");
                }
            }
        }

public static void main(String args[])throws InterruptedException {
        long patience = 1000 * 60 * 60;
        // If command line argument
        // present, gives patience
        // in seconds.
        if (args.length > 0) {
            try {
                patience = Long.parseLong(args[0]) * 1000;
            } catch (NumberFormatException e) {
                System.err.println("Argument must be an integer.");
                System.exit(1);
            }
        }

        threadMessage("Starting MessageLoop thread");
        long startTime = System.currentTimeMillis();
        Thread t = new Thread(new MessageLoop());
        t.start();

        threadMessage("Waiting for MessageLoop thread to finish");
        while (t.isAlive()) {
            threadMessage("Still waiting...");
            t.join(1000);
            if (((System.currentTimeMillis() - startTime) > patience)&& t.isAlive()) {
                threadMessage("Tired of waiting!");
                t.interrupt();
                t.join();
            }
        }
        threadMessage("Finally!");
    }
}
```
