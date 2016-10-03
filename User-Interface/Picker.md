# Picker
建议使用`DialogFragment`来创建时间选择器以及日期选择器。

## 时间选择器

### 创建时间选择器

1. 创建`DialogFragment`的子类，在`onCreateDialog`方法中返回`TimePickerDialog`的实例。要监听时间选择器的结果，需要实现`TimePickerDialog.OnTimeSetListener`接口。

``` java
  public static class TimePickerFragment extends DialogFragment
                              implements TimePickerDialog.OnTimeSetListener {

      @Override
      public Dialog onCreateDialog(Bundle savedInstanceState) {
          // Use the current time as the default values for the picker
          final Calendar c = Calendar.getInstance();
          int hour = c.get(Calendar.HOUR_OF_DAY);
          int minute = c.get(Calendar.MINUTE);

          // Create a new instance of TimePickerDialog and return it
          return new TimePickerDialog(getActivity(), this, hour, minute,
                  DateFormat.is24HourFormat(getActivity()));
      }

      public void onTimeSet(TimePicker view, int hourOfDay, int minute) {
          // Do something with the time chosen by the user
      }
  }
```

2. 在Activity中使用`show()`方法展示`TimePickerDialog`。（应使用FragmentActivity的子类）

``` java
  DialogFragment newFragment = new TimePickerFragment();
  newFragment.show(getSupportFragmentManager(), "timePicker");
```

**若不使用`DialogFragment`：**

``` java
TimePickerDialog timePickerDialog = new TimePickerDialog(MainActivity.this, new TimePickerDialog.OnTimeSetListener()
                {
                    @Override
                    public void onTimeSet(TimePicker view, int hourOfDay, int minute)
                    {
                        editText2.setText("Time: " + hourOfDay + ":" + minute);
                    }
                }, hourOfDay, minute, true);
timePickerDialog.show();
```

## 日期选择器

### 创建日期选择器

1. 创建`DialogFragment`的子类，在`onCreateDialog`方法中返回`DatePickerDialog`的实例。要监听日期选择器的结果，需要实现`DatePickerDialog.OnDateSetListene`接口。

   ``` java
   public static class DatePickerFragment extends DialogFragment
                               implements DatePickerDialog.OnDateSetListener {

       @Override
       public Dialog onCreateDialog(Bundle savedInstanceState) {
           // Use the current date as the default date in the picker
           final Calendar c = Calendar.getInstance();
           int year = c.get(Calendar.YEAR);
           int month = c.get(Calendar.MONTH);
           int day = c.get(Calendar.DAY_OF_MONTH);

           // Create a new instance of DatePickerDialog and return it
           return new DatePickerDialog(getActivity(), this, year, month, day);
       }

       public void onDateSet(DatePicker view, int year, int month, int day) {
           // Do something with the date chosen by the user
       }
   }
   ```

2. 在Activity中使用`show()`方法展示`DatePickerDialog`。（应使用FragmentActivity的子类）

   ``` java
   DialogFragment newFragment = new DatePickerFragment();
   newFragment.show(getSupportFragmentManager(), "datePicker");
   ```

**若不使用`DialogFragment`：**

``` java
DatePickerDialog datePickerDialog = new DatePickerDialog(MainActivity.this, new DatePickerDialog.OnDateSetListener()
                {
                    @Override
                    public void onDateSet(DatePicker view, int year, int monthOfYear,
                            int dayOfMonth)
                    {
                        editText.setText("日期：" + year + "-" + (monthOfYear + 1) + "-" + dayOfMonth);
                    }
                }, year, monthOfYear, dayOfMonth);
datePickerDialog.show();
```

