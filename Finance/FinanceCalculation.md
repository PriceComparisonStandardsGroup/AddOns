#Finance Calculation

Currently the standard supports two finance calculation plans, [standard](StandardPlan.md) and [level](LevelPlan.md).  Below is an example of calculating finance payments based on the aforementioned plans.

The example is intended to provide guidance on how to calculate payments and the precedence given to each calculation.  The aim is to minimise the possibilities of price variations between each PCW and their product provider partners.

This example code may be exercised by pasting into [dotnetfiddle](https://dotnetfiddle.net)

```c#
using System;

public class Program
{
	private const string Fixed = "Fixed";
	private const string Percentage  = "Percentage";
	public class CalculationResult
	{
		public decimal SinglePaymentTotal;
		public decimal FinanceDeposit;
		public int FinanceNumberOfInstallments;
		public decimal FinanceInstallmentAmount;
		public decimal FinanceTotalPayable;
	}
	
	public class Charge
	{
		public string FeeType;
		public string AmountType;
		public decimal Amount;
	}
	
	public class Charges
	{
		public Charge ApplyToDeposit;
		public Charge ApplyToLoan;
		public Charge ApplyToPremium;
	}
	
	
	public class Calculation 
	{ 
 		public decimal BasePremium;   			// Amount due for the primary insurance policy 
  		public decimal AddOnTotalPremium;		// The total sum of all add-ons selected by the customer
		public decimal MinDeposit;				// The minimum deposit amount 
		public decimal DepositRate;				// The deposit as percentage of the sum of BasePremium, add-ons and setup fees
		public decimal? MaxloanAmount;
  		public decimal MinLoanAmount;
		public int NoOfInstallments;
  		public decimal NetInterestRate;
		public decimal MinInterestAmount;
		public Charges FinanceCharges;
		
		public CalculationResult CalculateStandardPlan()
		{	
			
			decimal singlePaymentTotal = BasePremium + AddOnTotalPremium;
		 
				
			decimal applyToPremiumCharge = 0;
			
			if (FinanceCharges.ApplyToDeposit != null)
			{
				if (FinanceCharges.ApplyToPremium.AmountType == Fixed) { applyToPremiumCharge = FinanceCharges.ApplyToPremium.Amount; }
				if (FinanceCharges.ApplyToPremium.AmountType == Percentage) { applyToPremiumCharge = FinanceCharges.ApplyToPremium.Amount * singlePaymentTotal; }
			}		
			
			
			decimal premiumTotal = singlePaymentTotal + applyToPremiumCharge;
					
			
			decimal applyToDepositCharge = 0;
			
			if (FinanceCharges.ApplyToDeposit != null)
			{
				if (FinanceCharges.ApplyToDeposit.AmountType == Fixed) { applyToDepositCharge = FinanceCharges.ApplyToDeposit.Amount; }
				if (FinanceCharges.ApplyToDeposit.AmountType == Percentage) { applyToDepositCharge = FinanceCharges.ApplyToDeposit.Amount * premiumTotal; }
			}
			
			decimal deposit = Math.Max(MinDeposit, ((premiumTotal + applyToDepositCharge) * DepositRate));
			

			
			
			decimal applyToLoanCharge = 0;
			
			if (FinanceCharges.ApplyToLoan != null)
			{
				if (FinanceCharges.ApplyToLoan.AmountType == Fixed) { applyToLoanCharge = FinanceCharges.ApplyToLoan.Amount; }
				if (FinanceCharges.ApplyToLoan.AmountType == Percentage) { applyToLoanCharge = FinanceCharges.ApplyToLoan.Amount * (premiumTotal - deposit); }
			}			
									
			decimal loanAmount = premiumTotal - deposit + applyToLoanCharge;

			Console.WriteLine("loan amount = " + loanAmount);
				
			
			if (loanAmount > MaxloanAmount)
			{
				deposit = deposit + (loanAmount - MaxloanAmount.Value);
				
				loanAmount = MaxloanAmount.Value;
			}
			
			
			if (NoOfInstallments == 0 || loanAmount < MinLoanAmount || loanAmount > MaxloanAmount)
			{
				return new CalculationResult
				{
					SinglePaymentTotal = singlePaymentTotal
				};
			}


			decimal interestAmount = Math.Max(MinInterestAmount,loanAmount * NetInterestRate);
			
			Console.Write("interest amount = " + interestAmount);
			
			decimal installmentAmount = (loanAmount + interestAmount)/NoOfInstallments;
			
			
			var result = new CalculationResult
			{
				SinglePaymentTotal = singlePaymentTotal,
				FinanceDeposit = deposit,
				FinanceNumberOfInstallments = NoOfInstallments,
				FinanceInstallmentAmount = installmentAmount,
				FinanceTotalPayable = deposit + (NoOfInstallments * installmentAmount)				
			};
			
			return result;
		}
		
		
		public CalculationResult CalculateLevelPlan()
		{
					
			decimal singlePaymentTotal = BasePremium + AddOnTotalPremium;
		 
				
			decimal applyToPremiumCharge = 0;
			
			if (FinanceCharges.ApplyToDeposit != null)
			{
				if (FinanceCharges.ApplyToPremium.AmountType == Fixed) { applyToPremiumCharge = FinanceCharges.ApplyToPremium.Amount; }
				if (FinanceCharges.ApplyToPremium.AmountType == Percentage) { applyToPremiumCharge = FinanceCharges.ApplyToPremium.Amount * singlePaymentTotal; }
			}		
			
			
			decimal premiumTotal = singlePaymentTotal + applyToPremiumCharge;			
			
			
			
			
			decimal applyToLoanCharge = 0;
			
			if (FinanceCharges.ApplyToLoan != null)
			{
				if (FinanceCharges.ApplyToLoan.AmountType == Fixed) { applyToLoanCharge = FinanceCharges.ApplyToLoan.Amount; }
				if (FinanceCharges.ApplyToLoan.AmountType == Percentage) { applyToLoanCharge = FinanceCharges.ApplyToLoan.Amount * premiumTotal; }
			}			
									
			decimal loanAmount = premiumTotal + applyToLoanCharge;	
		
			//Console.WriteLine("loan amount = " + loanAmount);			
			
			
			
			if (NoOfInstallments == 0 || loanAmount < MinLoanAmount || loanAmount > MaxloanAmount)
			{
				return new CalculationResult
				{
					SinglePaymentTotal = singlePaymentTotal
				};
			}


            decimal interestAmount = Math.Max(MinInterestAmount,loanAmount * NetInterestRate);
			//Console.Write("interest amount = " + interestAmount);
			
			decimal installmentAmount = (loanAmount + interestAmount)/(NoOfInstallments + 1);
			
			decimal deposit = installmentAmount;
			
			var result = new CalculationResult
			{
				SinglePaymentTotal = singlePaymentTotal,
				FinanceDeposit = deposit,
				FinanceNumberOfInstallments = NoOfInstallments,
				FinanceInstallmentAmount = installmentAmount,
				FinanceTotalPayable = deposit + (NoOfInstallments * installmentAmount)
				
			};
			
			return result;
		}
	} 
		
	
	public static void Main()
	{
		Charge fee1 = new Charge
		{
			FeeType = "ApplyToDeposit",
			AmountType = Fixed,
			Amount = 0			
		};
		
	
		Charge fee2 = new Charge
		{
			FeeType = "ApplyToLoan",			
			AmountType = Fixed,
			Amount = 35			
		};	
		
			
		Charge fee3 = new Charge
		{
			FeeType = "ApplyToPremium",
			AmountType = Fixed,
			Amount = 12.99M			
		};
		
		var charges = new Charges
		{
			ApplyToDeposit = fee1,
			ApplyToLoan = fee2,
			ApplyToPremium = fee3
			
		};
		
		
		var calculation = new Calculation
		{
			BasePremium = 1000,
			AddOnTotalPremium = 0,
			MinDeposit  = 35,
			DepositRate = 0.30M,
			MaxloanAmount  = null,
			MinLoanAmount  = 0,
			NoOfInstallments  = 9,
			NetInterestRate  = 0.09M,
			MinInterestAmount = 0,
			FinanceCharges = charges
		};
		
		CalculationResult standardPlanResult = calculation.CalculateStandardPlan();
		Console.WriteLine();		
		Console.WriteLine("Standard Plan");
		Console.WriteLine($"  SinglePaymentTotal = {standardPlanResult.SinglePaymentTotal}");
		Console.WriteLine($"  FinanceDeposit = {standardPlanResult.FinanceDeposit}");
		Console.WriteLine($"  FinanceNumberOfInstallments = {standardPlanResult.FinanceNumberOfInstallments}");
		Console.WriteLine($"  FinanceInstallmentAmount = {standardPlanResult.FinanceInstallmentAmount}");
		Console.WriteLine($"  FinanceTotalPayable = {standardPlanResult.FinanceTotalPayable}");

		CalculationResult levelPlanResult = calculation.CalculateLevelPlan();
		Console.WriteLine();
		Console.WriteLine("Level Plan");		
		Console.WriteLine($"  SinglePaymentTotal = {levelPlanResult.SinglePaymentTotal}");
		Console.WriteLine($"  FinanceDeposit = {levelPlanResult.FinanceDeposit}");
		Console.WriteLine($"  FinanceNumberOfInstallments = {levelPlanResult.FinanceNumberOfInstallments}");
		Console.WriteLine($"  FinanceInstallmentAmount = {levelPlanResult.FinanceInstallmentAmount}");
		Console.WriteLine($"  FinanceTotalPayable = {levelPlanResult.FinanceTotalPayable}");
			
	}
}
```