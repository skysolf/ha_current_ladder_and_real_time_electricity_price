
Homeassistant 实时电价与当前用电阶梯


```
  
  - platform: template #平台名称
    sensors: #传感器列表
      real_time_electricity_price: #实体名称：只能用小写，下划线
        unique_id: "real_time_electricity_price" #UID（必须）
        friendly_name:  '实时电价' #在前端显示的传感器昵称（可选)
        unit_of_measurement: "CNY/kWh" #传感器数值的单位（可选）
        icon_template: mdi:currency-jpy #默认图标
        value_template: > #定义一个获取传感器状态的模板（必须）下面的6和22是指6点和22点，"1""2""3"是指阶梯123，6个价格分别是3个阶梯的峰谷价格
          {% if now().strftime("%H")| int >= 6 and now().strftime("%H")|int < 22 and states("sensor.current_ladder")=="1" %}
            0.617
          {%elif now().strftime("%H")| int >= 6 and now().strftime("%H")|int < 22 and states("sensor.current_ladder")=="2" %}
            0.677
          {%elif now().strftime("%H")| int >= 6 and now().strftime("%H")|int < 22 and states("sensor.current_ladder")=="3" %}
            0.977
          {% elif states("sensor.current_ladder")=="1" %}
            0.307
          {% elif states("sensor.current_ladder")=="2" %}
            0.337
          {% elif states("sensor.current_ladder")=="3" %}
            0.487
          {% endif %}
  
  - platform: template
    sensors:
      current_ladder:
        unique_id: "current_ladder"
        friendly_name:  '当前阶梯'
        unit_of_measurement: "级"
        icon_template: mdi:elevation-rise
        value_template: > #这里是上海的三个阶梯数值，第2阶梯3120，第三阶梯4800，为了统计准确，大家可以减去现如今已经用过的总度数
          {% if states("sensor.tasmota_energy_total") | float <= 3120 %}
          1
          {% elif states("sensor.tasmota_energy_total") | float >3120 and states("sensor.tasmota_energy_total") | float <= 4800 %}
          2
          {% else %}
          3
          {% endif %}


```
