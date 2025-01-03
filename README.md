# Retirement-Age-Calculator
حسابة سن التقاعد
import flet as ft
from datetime import datetime, timedelta

class RetirementCalculatorApp:
    def __init__(self, page: ft.Page):
        self.page = page
        self.page.title = "حاسبة سن التقاعد"
        
        self.create_widgets()

    def create_widgets(self):
        # Input fields for birth date and hire date
        self.birth_date_entry = ft.TextField(label="أدخل تاريخ ميلادك (DD-MM-YYYY):", text_align=ft.TextAlign.RIGHT)
        self.hire_date_entry = ft.TextField(label="أدخل تاريخ التعيين (DD-MM-YYYY):", text_align=ft.TextAlign.RIGHT)

        # Calculate button
        calculate_button = ft.ElevatedButton(text="احسب سن التقاعد", on_click=self.calculate_retirement_age)

        # Result label
        self.result_label = ft.Text(value="", max_lines=5, text_align=ft.TextAlign.RIGHT)

        # Buttons to show laws
        law_81_button = ft.ElevatedButton(text="عرض مواد قانون 81 لسنة 2016", on_click=self.show_law_81)
        pension_law_button = ft.ElevatedButton(text="عرض مواد قانون المعاشات المصري", on_click=self.show_pension_law)

        # Add widgets to the page
        self.page.add(
            self.birth_date_entry,
            self.hire_date_entry,
            calculate_button,
            self.result_label,
            law_81_button,
            pension_law_button
        )

    def calculate_retirement_age(self, e):
        try:
            # Parse inputs
            birth_date = datetime.strptime(self.birth_date_entry.value, "%d-%m-%Y")
            hire_date = datetime.strptime(self.hire_date_entry.value, "%d-%m-%Y")

            # Validate inputs
            if birth_date.year <= 0:
                raise ValueError("يرجى إدخال قيم صحيحة لتاريخ الميلاد.")

            # Determine retirement age and date
            retirement_age = self.get_retirement_age(birth_date.year)
            retirement_date = birth_date.replace(year=birth_date.year + retirement_age)

            # Calculate work duration until now
            work_duration = datetime.now() - hire_date
            work_details = self.format_duration(work_duration)

            # Calculate remaining time until retirement
            remaining_duration = retirement_date - datetime.now()
            remaining_details = self.format_duration(remaining_duration)

            self.result_label.value = (
                f"ستتقاعد في تاريخ {retirement_date.strftime('%Y-%m-%d')} في سن {retirement_age}.\n"
                f"مدة العمل حتى تاريخه: {work_details}.\n"
                f"المدة المتبقية للمعاش: {remaining_details}."
            )
            self.page.update()
        except ValueError as e:
            self.result_label.value = f"خطأ: {e}"
            self.page.update()

    def get_retirement_age(self, birth_year):
        if birth_year <= 1970:
            return 60
        elif birth_year > 1970:
            additional_years = min(65 - 60, birth_year - 1970)
            return 60 + additional_years

    @staticmethod
    def format_duration(duration):
        days = duration.days
        seconds = duration.seconds
        hours = seconds // 3600
        minutes = (seconds % 3600) // 60
        seconds = seconds % 60
        return f"{days} يوم، {hours} ساعة، {minutes} دقيقة، {seconds} ثانية"

    def show_law_81(self, e):
        law_81_text = (
            "قانون 81 لسنة 2016:\n"
            "المادة 70: يكون سن الإحالة للمعاش بالنسبة لشاغلي الوظائف القيادية ووظائف الإدارة العليا 60 سنة.\n"
            "المادة 71: يجوز مد الخدمة لشاغلي الوظائف القيادية ووظائف الإدارة العليا بعد بلوغ سن الإحالة للمعاش لمدة لا تجاوز ثلاث سنوات.\n"
            "المادة 72: يجوز بقرار من رئيس مجلس الوزراء بناءً على عرض الوزير المختص مد سن الإحالة للمعاش إلى 65 سنة لبعض الفئات من العاملين وفقًا للضوابط التي يحددها القرار.\n"
        )
        self.result_label.value = law_81_text
        self.page.update()

    def show_pension_law(self, e):
        pension_law_text = (
            "قانون المعاشات المصري:\n"
            "المادة 41: يستحق المؤمن عليه المعاش عند بلوغ سن الشيخوخة مع توافر مدة اشتراك في تأمين الشيخوخة والعجز والوفاة لا تقل عن 120 شهرًا.\n"
            "المادة 42: يجوز للمؤمن عليه الذي بلغ سن الشيخوخة ولم تتوافر فيه مدة الاشتراك المشار إليها في المادة 41 أن يستمر في الاشتراك في التأمين حتى استكمال هذه المدة.\n"
            "المادة 43: يجوز بقرار من رئيس مجلس الوزراء بناءً على عرض الوزير المختص مد سن الإحالة للمعاش إلى 65 سنة لبعض الفئات من العاملين وفقًا للضوابط التي يحددها القرار.\n"
        )
        self.result_label.value = pension_law_text
        self.page.update()

def main(page: ft.Page):
    app = RetirementCalculatorApp(page)

ft.app(target=main)
