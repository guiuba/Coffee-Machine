# Coffee-Machine
Coffee-Machine is a Jet Brains Academy project. 

This is my implementation for stage 6/6.

How it works:
![image](https://user-images.githubusercontent.com/69851038/117159389-9c112100-ad96-11eb-9e75-e7851fd0be18.png)

-------------------------------------------------
My code:

import java.util.Scanner;

public class CoffeeMachine {

	static Scanner scan = new Scanner(System.in);
	static State currentState = State.STANDBY;
	static int waterStock = 400;
	static int milkStock = 540;
	static int coffeeBeansStock = 120;
	static int disposablecupsStock = 9;
	static int moneyStock= 550;

	public static void main(String[] args) {
		do {
			System.out.println(getAction());
		} while (processCommand(scan.nextLine().toLowerCase()));


		scan.close();
	}

	static String getAction() {

		switch(currentState) {	

		case BUY:
			return "What do you want to buy? 1 - espresso, 2 - latte, 3 - cappuccino, back - to main menu:";

		case FILL:
			fillCoffeeMachine();
			currentState = State.STANDBY;
			return "";

		case STANDBY:
		default:
			return "Write action (buy, fill, take, remaining, exit): ";
		}
	}

	private static boolean processCommand(String command) {
		switch (currentState) {
		case STANDBY:
			switch (command) {
			case "buy":
				currentState = State.BUY;
				return true;
			case "fill":
				currentState = State.FILL;
				return true;
			case "take":
				takeMoney();
				return true;
			case "remaining":
				displayRemainingStock();
				return true;
			case "exit":
				return false;

			default:
				break;
			}

		case BUY:
			try {
				checkResources(CoffeeRecipes.getCoffeeByOption(Integer.parseInt(command)));		
			} catch (IllegalArgumentException e) {}
			currentState = State.STANDBY;
			return true;

		default:
			break;
		}
		return true;
	}

	enum State {
		STANDBY, BUY, FILL, TAKE, EXIT;
	}

	enum CoffeeRecipes {
		ESPRESSO(1, 250, 0, 16, 4), LATTE(2, 350, 75, 20, 7), CAPPUCCINO(3, 200, 100, 12, 6);
		int option;
		int water;
		int milk;
		int coffeeBeans;
		int price;

		CoffeeRecipes(int option, int water, int milk, int coffeeBeans, int price) {
			this.option = option;
			this.water = water;
			this.milk = milk;
			this.coffeeBeans = coffeeBeans;
			this.price = price;
		}

		public int getOption() {
			return option;
		}

		public int getWater() {
			return water;
		}

		public int getMilk() {
			return milk;
		}

		public int getCoffeeBeans() {
			return coffeeBeans;
		}

		public int getPrice() {
			return price;
		}

		public static CoffeeRecipes getCoffeeByOption(int option) {
			for(CoffeeRecipes recipe : CoffeeRecipes.values()) {
				if(recipe.getOption() == option) {
					return recipe;
				}
			}
			throw new IllegalArgumentException();
		}

	}

	static void takeMoney() {		
		System.out.printf("I gave you $%d\n", moneyStock);
		moneyStock = 0;
	}

	static void fillCoffeeMachine() {
		System.out.println("Write how many ml of water do you want to add: ");
		waterStock += validateEntry();
		System.out.println("Write how many ml of milk do you want to add: ");
		milkStock += validateEntry();
		System.out.println("Write how many grams of coffee beans do you want to add: ");
		coffeeBeansStock += validateEntry();
		System.out.println("Write how many disposable cups of coffee do you want to add: ");
		disposablecupsStock += validateEntry();
	}

	static int validateEntry() {
		boolean isValidEntry = false;
		int input = 0;
		while (!isValidEntry) {
			try {
				System.out.println("Enter a number");
				input = Integer.parseInt(scan.next());
				isValidEntry = true;
				break;
			} catch (NumberFormatException e) {
				System.out.println("Invalid entry. Try again.");
			}
		}
		return input;
	}

	static void checkResources(CoffeeRecipes recipe) {
		if (recipe.getWater() > waterStock ){
			System.out.println("Sorry, not enough water!");
		} else if (recipe.getMilk() > milkStock ){
			System.out.println("Sorry, not enough milk!");
		} else if (recipe.getCoffeeBeans() > coffeeBeansStock){
			System.out.println("Sorry, not enough  coffee beans!");
		} else if (disposablecupsStock == 0){
			System.out.println("Sorry, not enough  disposable cups!");
		} else {
			System.out.println("I have enough resources, making you a coffee!");
			waterStock -= recipe.getWater();
			milkStock -= recipe.getMilk();
			coffeeBeansStock -= recipe.getCoffeeBeans();
			disposablecupsStock--;
			moneyStock += recipe.getPrice();
		}

	}

	static void displayRemainingStock() {
		System.out.printf(
				"The coffee machine has: \n" +
						"%d of water\n" +
						"%d of milk\n" +
						"%d of coffee beans\n" +
						"%d of disposable cups\n" +
						"$%d of money\n\n", waterStock, milkStock, coffeeBeansStock, disposablecupsStock, moneyStock);
	}
}


