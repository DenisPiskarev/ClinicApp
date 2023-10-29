# ClinicApp
## Описание исправлений:
### 1. В карточке пациента можно добавить дату рождения в будущем.
Ошибка обнаружена в файле AddModifyPatient.xaml.cs в обработчике btnSubmit_Click. Отсутствовала проверка на некорректный ввод. Добавил проверку:
```cs
if (dateOfBirth >= DateTime.Now || dateOfBirth < new DateTime(1753, 1, 1))
{
    status = false;
    messageBuilder.Append("Дата рождения некорректна.\n");
}
```
### 2. При вводе даты рождения 00.00.0001 приложение падает.
Ошибка обнаружена в файле AddModifyPatient.xaml.cs в обработчике btnSubmit_Click. Тип данных DATETIME в MS SQL Server хранит даты и время от 01/01/1753 до 31/12/9999. В связи с этим добавил проверку на некорректное значение:
```cs
if (dateOfBirth >= DateTime.Now || dateOfBirth < new DateTime(1753, 1, 1))
{
    status = false;
    messageBuilder.Append("Дата рождения некорректна.\n");
}
```
### 3. При редактировании существующего обращения пациента, приложение падает. 
Ошибка обнаружена в файле AddModifyRequest.xaml.cs в обработчике btnSubmit_Click. При создании обновленного обращения теряются данные пациента. Удалена строка: 
```cs
m_request.Patient = new PatientCard();
```
## Добавление столбца "Возраст" на форму со списком пациентов.
Добавлен конвертер возраста: 
```cs
    public class AgeConverter : IMultiValueConverter
    {
        public object Convert(object[] values, Type targetType, object parameter, CultureInfo culture)
        {
            if (values[0] is DateTime dateOfBirth)
            {
                int age = DateTime.Today.Year - dateOfBirth.Year;
                if (DateTime.Today < dateOfBirth.AddYears(age))
                {
                    age--;
                }
                return age.ToString();
            }
            return null;
        }

        public object[] ConvertBack(object value, Type[] targetTypes, object parameter, CultureInfo culture)
        {
            throw new NotImplementedException();
        }
    }
```
Добавлен столбец "Возраст":
```xaml
                    <DataGridTextColumn Header="Возраст" >
                        <DataGridTextColumn.Binding>
                            <MultiBinding Converter="{StaticResource AgeConverter}">
                                <Binding Path="DateOfBirth" />
                            </MultiBinding>
                        </DataGridTextColumn.Binding>
                    </DataGridTextColumn>
```
