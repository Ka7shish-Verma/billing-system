from tkinter import *
from tkinter import messagebox
import random
import os

class BillingSystem:
    def __init__(self,root):
        self.root = root
        self.root.title("Billing System")
        self.root.geometry("1000x600")

        # ==== Variables ====
        self.c_name = StringVar()
        self.c_phone = StringVar()
        self.bill_no = StringVar()
        self.bill_no.set(str(random.randint(1000,9999)))

        self.items = StringVar()
        self.price = StringVar()
        self.qty = StringVar()

        self.total = 0
        self.product_list = []

        # ===== Title =====
        title = Label(self.root,text="🛒 Billing System",font=("Arial",25,"bold"),bg="darkblue",fg="white",pady=10)
        title.pack(fill=X)

        # ===== Customer Frame =====
        f1 = LabelFrame(self.root,text="Customer Details",font=("Arial",12,"bold"))
        f1.place(x=10,y=70,width=400,height=120)

        Label(f1,text="Customer Name").grid(row=0,column=0,padx=5,pady=5)
        Entry(f1,textvariable=self.c_name).grid(row=0,column=1,padx=5,pady=5)

        Label(f1,text="Phone No").grid(row=1,column=0,padx=5,pady=5)
        Entry(f1,textvariable=self.c_phone).grid(row=1,column=1,padx=5,pady=5)

        Label(f1,text="Bill No").grid(row=2,column=0,padx=5,pady=5)
        Entry(f1,textvariable=self.bill_no).grid(row=2,column=1,padx=5,pady=5)

        # ===== Product Frame =====
        f2 = LabelFrame(self.root,text="Product Details",font=("Arial",12,"bold"))
        f2.place(x=10,y=200,width=400,height=200)

        Label(f2,text="Item Name").grid(row=0,column=0,padx=5,pady=5)
        Entry(f2,textvariable=self.items).grid(row=0,column=1,padx=5,pady=5)

        Label(f2,text="Price").grid(row=1,column=0,padx=5,pady=5)
        Entry(f2,textvariable=self.price).grid(row=1,column=1,padx=5,pady=5)

        Label(f2,text="Quantity").grid(row=2,column=0,padx=5,pady=5)
        Entry(f2,textvariable=self.qty).grid(row=2,column=1,padx=5,pady=5)

        Button(f2,text="Add Item",command=self.add_item,bg="green",fg="white").grid(row=3,column=0,pady=10)
        Button(f2,text="Clear",command=self.clear,bg="gray",fg="white").grid(row=3,column=1,pady=10)

        # ===== Bill Area =====
        f3 = Frame(self.root,bd=3,relief=GROOVE)
        f3.place(x=430,y=70,width=550,height=430)

        Label(f3,text="Bill Area",font=("Arial",13,"bold")).pack(fill=X)
        scroll = Scrollbar(f3,orient=VERTICAL)
        self.textarea = Text(f3,yscrollcommand=scroll.set)
        scroll.pack(side=RIGHT,fill=Y)
        self.textarea.pack(fill=BOTH,expand=1)
        scroll.config(command=self.textarea.yview)

        # ===== Bottom Buttons =====
        f4 = Frame(self.root)
        f4.place(x=10,y=420,width=400,height=190)

        Button(f4,text="Generate Bill",command=self.generate_bill,bg="blue",fg="white",width=12).grid(row=0,column=0,padx=5,pady=10)
        Button(f4,text="Save Bill",command=self.save_bill,bg="purple",fg="white",width=12).grid(row=0,column=1)
        Button(f4,text="Exit",command=self.exit_app,bg="red",fg="white",width=12).grid(row=0,column=2,padx=5)

        self.welcome()

    def welcome(self):
        self.textarea.delete(1.0,END)
        self.textarea.insert(END,"\tWelcome To Billing System\n")
        self.textarea.insert(END,f"Bill No: {self.bill_no.get()}\n")
        self.textarea.insert(END,f"Customer Name: {self.c_name.get()}\n")
        self.textarea.insert(END,f"Phone: {self.c_phone.get()}\n")
        self.textarea.insert(END,"-----------------------------------------\n")
        self.textarea.insert(END,"Product\tQty\tPrice\n")
        self.textarea.insert(END,"-----------------------------------------\n")

    def add_item(self):
        try:
            price = float(self.price.get())
            qty = int(self.qty.get())
            total = price * qty
            self.product_list.append([self.items.get(), qty, total])
            self.textarea.insert(END,f"{self.items.get()}\t{qty}\t{total}\n")
            self.total += total
        except:
            messagebox.showerror("Error","Invalid Input")

    def generate_bill(self):
        self.textarea.insert(END,"-----------------------------------------\n")
        self.textarea.insert(END,f"Total Bill: ₹{self.total}\n")
        tax = round(self.total * 0.05,2)
        net = self.total + tax
        self.textarea.insert(END,f"GST (5%): ₹{tax}\n")
        self.textarea.insert(END,f"Net Amount: ₹{net}\n")
        self.textarea.insert(END,"-----------------------------------------\n")

    def save_bill(self):
        bill_data = self.textarea.get(1.0,END)
        with open(f"Bill_{self.bill_no.get()}.txt","w") as f:
            f.write(bill_data)
        messagebox.showinfo("Saved",f"Bill No. {self.bill_no.get()} Saved Successfully")

    def clear(self):
        self.items.set("")
        self.price.set("")
        self.qty.set("")

    def exit_app(self):
        self.root.destroy()

root = Tk()
obj = BillingSystem(root)
root.mainloop()
