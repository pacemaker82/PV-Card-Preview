# PV-Card-Preview

<img width="500" height="111" alt="Screenshot 2025-07-25 at 09 54 34" src="https://github.com/user-attachments/assets/2d4ae4a5-2e2b-4bf4-8750-24a0ab1b1e01" />

Card YAML to show the predicted PV for the next 5 days. The card will grow/shrink the bar relative to your maximum daily PV, and change colour (red) when the predicted PV is less than your minimum "target".

Check out my blog post on [how I use this card within my dashboard](https://smarter-home.blog/2025/09/01/home-assistant-dashboard-for-solar-pv-tracking/).

This visualisation uses the following cards from HACS:

- config-template-card (To enable javascript variables - found in HACS)
- bar-card (To show the bar card graphics - found in HACS)
- card_mod (To manipulate the CSS in the bar card - found in HACS)

As well as:
- Predbat or Solcast (For the PV solar prediction entities)

## Instructions

[1] Install Predbat here https://github.com/springfall2008/batpred if needed

[2] Add the following helper entities in your configuration.yaml. Restart HA

```
input_number:    
  pv_min_threshold:
    name: Minimum PV Threshold
    min: 0
    max: 100
    step: 1
    unit_of_measurement: "kWh"
    mode: box

  pv_max_threshold:
    name: Maximum PV Threshold
    min: 0
    max: 100
    step: 1
    unit_of_measurement: "kWh"
    mode: box
```

[3] Goto the new helper entities and set your daily minimum PV kWh (where anything less would not be ideal), and your daily maximum PV kWh (daily maximum you can realistically achieve)

[4] Install all the above cards from HACS 

[5] (Optional) Change the entities referenced in each card section in the code below. You can use any entities here from any integration, as long as they're providing a forecast of PV in kWh.

Here is the entire YAML blob you need to create the cards in the image above. You should just be able to copy/paste it in.

```
type: custom:config-template-card
variables:
  MAX_PV: states['input_number.pv_max_threshold'].state
  getLabel: |
    dayToGet => {
      const days = ["Sun", "Mon", "Tues", "Wed", "Thur", "Fri", "Sat"];
      const today = new Date();
      const tomorrow = new Date(today);
      tomorrow.setDate(today.getDate() + dayToGet);      
      return days[tomorrow.getDay()]; 
    }
entities:
  - sensor.predbat_pv_today
card:
  type: horizontal-stack
  cards:
    - type: custom:bar-card
      max: ${MAX_PV}
      height: 100px
      direction: up
      color: var(--energy-solar-color)
      name: Today
      unit_of_measurement: " "
      decimal: 2
      positions:
        icon: "off"
        indicator: "off"
      entities:
        - entity: sensor.predbat_pv_today
      card_mod:
        style: >-
          {% set energy_raw = states('sensor.predbat_pv_today') | float -%}            
          {% set min_pv = states('input_number.pv_min_threshold') | float -%}            
          {% set max_pv = states('input_number.pv_max_threshold') | float -%}            
          #states {
            padding: 0px;
            box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
            {%- if energy_raw < min_pv -%}
              box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
            {%- endif %}               
            border-radius: 10px;
          }

          bar-card-currentbar {
            border-radius: 7px !important;
            width: calc(100% - 6px) !important;
            margin-left: 3px;          
            margin-top: 3px;
            height: calc(100% - 6px) !important;
            {% set energy = ((energy_raw / max_pv) + 0.08) * 100 -%}
              opacity: {{ energy }}%; 
            {%- if energy_raw < min_pv -%}
              --bar-color: #ff3333 !important;
            {%- endif %}                 
          }

          bar-card-contentbar {
            border-radius: 7px !important;
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;         
          }

          bar-card-backgroundbar {
            border-radius: 7px !important;            
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;   
          }

          bar-card-value {
            color: #FFFFFF;
            font-weight: bold;
            font-size: 25px;
            text-shadow: 0px 1px 2px #333333;
            margin: 24px;
            text-align: center;
            line-height: 18px;
          }  

          bar-card-value::after {
            content: "kWh";  /* Adds the " kWh" text */
            display: block;
            white-space: pre;
            font-size: 10px;
          }   

          bar-card-name {
            color: var(--primary-text-color);              
            text-shadow: 0px 1px 2px var(--card-background-color);
            font-size: 14px;
          }   

          bar-card-markerbar {
            background-color: white;
            filter: brightness(1.0);  
            width: calc(100% - 6px) !important;        
            margin-left: 3px;
            height: 2px !important;
          }

          bar-card-targetbar {
            width: calc(100% - 6px) !important;                        
            margin-left: 3px;
          }
    - type: custom:bar-card
      max: ${MAX_PV}
      height: 100px
      direction: up
      color: var(--energy-solar-color)
      name: ${getLabel(1)}
      unit_of_measurement: " "
      decimal: 2
      positions:
        icon: "off"
        indicator: "off"
      entities:
        - entity: sensor.predbat_pv_tomorrow
      card_mod:
        style: >-
          {% set energy_raw = states('sensor.predbat_pv_tomorrow') | float -%}            
          {% set min_pv = states('input_number.pv_min_threshold') | float -%}            
          {% set max_pv = states('input_number.pv_max_threshold') | float -%}            
          #states {
            padding: 0px;
            box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
            {%- if energy_raw < min_pv -%}
              box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
            {%- endif %}               
            border-radius: 10px;
          }

          bar-card-currentbar {
            border-radius: 7px !important;
            width: calc(100% - 6px) !important;
            margin-left: 3px;          
            margin-top: 3px;
            height: calc(100% - 6px) !important;
            {% set energy = ((energy_raw / max_pv) + 0.08) * 100 -%}
              opacity: {{ energy }}%; 
            {%- if energy_raw < min_pv -%}
              --bar-color: #ff3333 !important;
            {%- endif %}                 
          }

          bar-card-contentbar {
            border-radius: 7px !important;
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;         
          }

          bar-card-backgroundbar {
            border-radius: 7px !important;            
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;   
          }

          bar-card-value {
            color: #FFFFFF;
            font-weight: bold;
            font-size: 25px;
            text-shadow: 0px 1px 2px #333333;
            margin: 24px;
            text-align: center;
            line-height: 18px;
          }  

          bar-card-value::after {
            content: "kWh";  /* Adds the " kWh" text */
            display: block;
            white-space: pre;
            font-size: 10px;
          }   

          bar-card-name {
            color: var(--primary-text-color);             
            text-shadow: 0px 1px 2px var(--card-background-color);
            font-size: 14px;
          }   

          bar-card-markerbar {
            background-color: white;
            filter: brightness(1.0);  
            width: calc(100% - 6px) !important;        
            margin-left: 3px;
            height: 2px !important;
          }

          bar-card-targetbar {
            width: calc(100% - 6px) !important;                        
            margin-left: 3px;
          }          
    - type: custom:bar-card
      max: ${MAX_PV}
      height: 100px
      direction: up
      color: var(--energy-solar-color)
      name: ${getLabel(2)}
      unit_of_measurement: " "
      decimal: 2
      positions:
        icon: "off"
        indicator: "off"
      entities:
        - entity: sensor.predbat_pv_d2
      card_mod:
        style: >-
          {% set energy_raw = states('sensor.predbat_pv_d2') | float -%}            
          {% set min_pv = states('input_number.pv_min_threshold') | float -%}            
          {% set max_pv = states('input_number.pv_max_threshold') | float -%}            
          #states {
            padding: 0px;
            box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
            {%- if energy_raw < min_pv -%}
              box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
            {%- endif %}               
            border-radius: 10px;
          }

          bar-card-currentbar {
            border-radius: 7px !important;
            width: calc(100% - 6px) !important;
            margin-left: 3px;          
            margin-top: 3px;
            height: calc(100% - 6px) !important;
            {% set energy = ((energy_raw / max_pv) + 0.08) * 100 -%}
              opacity: {{ energy }}%; 
            {%- if energy_raw < min_pv -%}
              --bar-color: #ff3333 !important;
            {%- endif %}                 
          }

          bar-card-contentbar {
            border-radius: 7px !important;
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;         
          }

          bar-card-backgroundbar {
            border-radius: 7px !important;            
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;   
          }

          bar-card-value {
            color: #FFFFFF;
            font-weight: bold;
            font-size: 25px;
            text-shadow: 0px 1px 2px #333333;
            margin: 24px;
            text-align: center;
            line-height: 18px;
          }  

          bar-card-value::after {
            content: "kWh";  /* Adds the " kWh" text */
            display: block;
            white-space: pre;
            font-size: 10px;
          }   

          bar-card-name {
            color: var(--primary-text-color);             
            text-shadow: 0px 1px 2px var(--card-background-color);
            font-size: 14px;
          }   

          bar-card-markerbar {
            background-color: white;
            filter: brightness(1.0);  
            width: calc(100% - 6px) !important;        
            margin-left: 3px;
            height: 2px !important;
          }

          bar-card-targetbar {
            width: calc(100% - 6px) !important;                        
            margin-left: 3px;
          }
    - type: custom:bar-card
      max: ${MAX_PV}
      height: 100px
      direction: up
      color: var(--energy-solar-color)
      name: ${getLabel(3)}
      unit_of_measurement: " "
      decimal: 2
      positions:
        icon: "off"
        indicator: "off"
      entities:
        - entity: sensor.predbat_pv_d3
      card_mod:
        style: >-
          {% set energy_raw = states('sensor.predbat_pv_d3') | float -%}            
          {% set min_pv = states('input_number.pv_min_threshold') | float -%}            
          {% set max_pv = states('input_number.pv_max_threshold') | float -%}            
          #states {
            padding: 0px;
            box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
            {%- if energy_raw < min_pv -%}
              box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
            {%- endif %}               
            border-radius: 10px;
          }

          bar-card-currentbar {
            border-radius: 7px !important;
            width: calc(100% - 6px) !important;
            margin-left: 3px;          
            margin-top: 3px;
            height: calc(100% - 6px) !important;
            {% set energy = ((energy_raw / max_pv) + 0.08) * 100 -%}
              opacity: {{ energy }}%; 
            {%- if energy_raw < min_pv -%}
              --bar-color: #ff3333 !important;
            {%- endif %}                 
          }

          bar-card-contentbar {
            border-radius: 7px !important;
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;         
          }

          bar-card-backgroundbar {
            border-radius: 7px !important;            
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;   
          }

          bar-card-value {
            color: #FFFFFF;
            font-weight: bold;
            font-size: 25px;
            text-shadow: 0px 1px 2px #333333;
            margin: 24px;
            text-align: center;
            line-height: 18px;
          }  

          bar-card-value::after {
            content: "kWh";  /* Adds the " kWh" text */
            display: block;
            white-space: pre;
            font-size: 10px;
          }   

          bar-card-name {
            color: var(--primary-text-color);              
            text-shadow: 0px 1px 2px var(--card-background-color);
            font-size: 14px;
          }   

          bar-card-markerbar {
            background-color: white;
            filter: brightness(1.0);  
            width: calc(100% - 6px) !important;        
            margin-left: 3px;
            height: 2px !important;
          }

          bar-card-targetbar {
            width: calc(100% - 6px) !important;                        
            margin-left: 3px;
          }



    - type: custom:bar-card
      max: ${MAX_PV}
      height: 100px
      direction: up
      color: var(--energy-solar-color)
      name: ${getLabel(4)}
      unit_of_measurement: " "
      decimal: 2
      positions:
        icon: "off"
        indicator: "off"
      entities:
        - entity: sensor.predbat_pv_d4
      card_mod:
        style: >-
          {% set energy_raw = states('sensor.predbat_pv_d4') | float -%}            
          {% set min_pv = states('input_number.pv_min_threshold') | float -%}            
          {% set max_pv = states('input_number.pv_max_threshold') | float -%}            
          #states {
            padding: 0px;
            box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
            {%- if energy_raw < min_pv -%}
              box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
            {%- endif %}               
            border-radius: 10px;
          }

          bar-card-currentbar {
            border-radius: 7px !important;
            width: calc(100% - 6px) !important;
            margin-left: 3px;          
            margin-top: 3px;
            height: calc(100% - 6px) !important;
            {% set energy = ((energy_raw / max_pv) + 0.08) * 100 -%}
              opacity: {{ energy }}%; 
            {%- if energy_raw < min_pv -%}
              --bar-color: #ff3333 !important;
            {%- endif %}                 
          }

          bar-card-contentbar {
            border-radius: 7px !important;
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;         
          }

          bar-card-backgroundbar {
            border-radius: 7px !important;            
            margin-left: 3px;
            width: calc(100% - 6px) !important;
            height: calc(100% - 6px) !important;
            margin-top: 3px;   
          }

          bar-card-value {
            color: #FFFFFF;
            font-weight: bold;
            font-size: 25px;
            text-shadow: 0px 1px 2px #333333;
            margin: 24px;
            text-align: center;
            line-height: 18px;
          }  

          bar-card-value::after {
            content: "kWh";  /* Adds the " kWh" text */
            display: block;
            white-space: pre;
            font-size: 10px;
          }   

          bar-card-name {
            color: var(--primary-text-color);              
            text-shadow: 0px 1px 2px var(--card-background-color);
            font-size: 14px;
          }   

          bar-card-markerbar {
            background-color: white;
            filter: brightness(1.0);  
            width: calc(100% - 6px) !important;        
            margin-left: 3px;
            height: 2px !important;
          }

          bar-card-targetbar {
            width: calc(100% - 6px) !important;                        
            margin-left: 3px;
          }
```
