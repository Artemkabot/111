import tkinter as tk
from tkinter import ttk
from tkinter import messagebox

# Данные вынесены в константы для лучшей организации и производительности
# (словари не создаются заново при каждом вызове функции)
LICENSE_DATA = {
    '1': {"name": "Cloud Hosted MWS-CP", "price": 1500, "vat_price": 1800, "period": "месяц"},
    '2': {"name": "Cloud Hosted MWS-CP Data Services", "price": 1100, "vat_price": 1320, "period": "месяц"},
    '3': {"name": "On-prem Hosted MWS-CP", "price": 36000, "vat_price": 43200, "period": "год"},
    '4': {"name": "On-prem Hosted MWS-CP Data Services", "price": 26400, "vat_price": 31680, "period": "год"}
}
DISCOUNTS = {'1': 0.30, '2': 0.27, '3': 0.25}

def calculate_license_cost(license_type, vcpu_count, partner_tier):
    """
    Выполняет расчеты и возвращает отформатированную строку с результатом.
    """
    selected_license = LICENSE_DATA[license_type]
    discount = DISCOUNTS[partner_tier]

    base_cost = selected_license['price'] * vcpu_count
    partner_revenue = base_cost * (1 - discount)
    client_cost = partner_revenue * 1.2
    base_client_cost = selected_license['vat_price'] * vcpu_count
    
    period = selected_license['period']
    discount_percent = int(discount * 100)

    result_lines = [
        f"РАСЧЕТ ДЛЯ: {selected_license['name']} ({vcpu_count} vCPU)",
        f"ПАРТНЕРСКИЙ ТАРИФ: {discount_percent}% скидка",
        "=" * 50,
        f"Базовая стоимость ({period}): {base_client_cost:,.0f} руб. (с НДС)",
        f"Скидка партнера: {discount_percent}%",
        "-" * 50,
        f"ИТОГО ДЛЯ КЛИЕНТА: {client_cost:,.0f} руб. (с НДС)",
        f"REVENUE ПАРТНЕРА: {partner_revenue:,.0f} руб. (без НДС)",
        "=" * 50
    ]
    return "\n".join(result_lines)

class LicenseCalculatorApp(tk.Tk):
    def __init__(self):
        super().__init__()
        
        self.title("Калькулятор лицензий")
        self.geometry("600x650") # Увеличен размер окна для комфортного отображения
        self.minsize(550, 500)   # Минимальный размер окна

        self._setup_styles()
        self._create_variables()
        self._create_widgets()

    def _setup_styles(self):
        style = ttk.Style(self)
        style.configure("TLabel", padding=6, font=('Helvetica', 10))
        style.configure("TButton", padding=6, font=('Helvetica', 10, 'bold'))
        style.configure("TFrame", padding=10)
        style.configure("TLabelframe", padding=10)
        style.configure("TLabelframe.Label", font=('Helvetica', 11, 'bold'))

    def _create_variables(self):
        self.license_type_var = tk.StringVar(value='1')
        self.vcpu_var = tk.StringVar(value='1')
        self.partner_tier_var = tk.StringVar(value='1')

    def _create_widgets(self):
        main_frame = ttk.Frame(self, padding="10")
        main_frame.pack(fill=tk.BOTH, expand=True)

        # Конфигурация сетки: столбец 0 будет расширяться
        main_frame.grid_columnconfigure(0, weight=1)
        # Строка 4 (с результатом) будет расширяться по вертикали
        main_frame.grid_rowconfigure(4, weight=1)

        # 1. Тип лицензии
        license_frame = ttk.LabelFrame(main_frame, text="1. Выберите тип лицензии")
        license_frame.grid(row=0, column=0, sticky="ew", pady=5)
        licenses = [
            ("Cloud Hosted MWS-CP", "1"), ("Cloud Hosted MWS-CP Data Services", "2"),
            ("On-prem Hosted MWS-CP", "3"), ("On-prem Hosted MWS-CP Data Services", "4"),
        ]
        for i, (text, value) in enumerate(licenses):
            ttk.Radiobutton(license_frame, text=text, variable=self.license_type_var, value=value).pack(anchor=tk.W)

        # 2. Количество vCPU
        vcpu_frame = ttk.LabelFrame(main_frame, text="2. Введите количество vCPU")
        vcpu_frame.grid(row=1, column=0, sticky="ew", pady=5)
        vcpu_entry = ttk.Entry(vcpu_frame, textvariable=self.vcpu_var, width=10, font=('Helvetica', 10))
        vcpu_entry.pack()

        # 3. Партнерский тариф
        partner_frame = ttk.LabelFrame(main_frame, text="3. Выберите партнерский тариф")
        partner_frame.grid(row=2, column=0, sticky="ew", pady=5)
        partners = [("Professional (30%)", "1"), ("Expert (27%)", "2"), ("Basic (25%)", "3")]
        for text, value in partners:
            ttk.Radiobutton(partner_frame, text=text, variable=self.partner_tier_var, value=value).pack(anchor=tk.W)
            
        # Кнопка Расчета
        calc_button = ttk.Button(main_frame, text="Рассчитать", command=self.display_result)
        calc_button.grid(row=3, column=0, pady=10)

        # Область для вывода результата: заменен Label на Text
        self.result_text = tk.Text(
            main_frame, height=10, font=('Courier', 10), wrap='word',
            background="white", relief="sunken", borderwidth=2, padx=10, pady=10
        )
        self.result_text.grid(row=4, column=0, sticky="nsew")
        self.result_text.config(state='disabled') # Сделать поле только для чтения

    def display_result(self):
        try:
            vcpu_count = int(self.vcpu_var.get())
            if vcpu_count <= 0:
                messagebox.showerror("Ошибка ввода", "Количество vCPU должно быть положительным числом.")
                return

            result_text_content = calculate_license_cost(
                self.license_type_var.get(),
                vcpu_count,
                self.partner_tier_var.get()
            )
            
            # Обновление содержимого виджета Text
            self.result_text.config(state='normal')       # Разрешить редактирование
            self.result_text.delete('1.0', tk.END)        # Очистить старый текст
            self.result_text.insert(tk.END, result_text_content) # Вставить новый текст
            self.result_text.config(state='disabled')     # Снова запретить редактирование

        except ValueError:
            messagebox.showerror("Ошибка ввода", "Пожалуйста, введите корректное целое число для vCPU.")
        except Exception as e:
            messagebox.showerror("Неизвестная ошибка", f"Произошла ошибка: {e}")


if __name__ == "__main__":
    app = LicenseCalculatorApp()
    app.mainloop()
