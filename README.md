# PV-Card-Preview

<img width="500" height="111" alt="Screenshot 2025-07-25 at 09 54 34" src="https://github.com/user-attachments/assets/2d4ae4a5-2e2b-4bf4-8750-24a0ab1b1e01" />

Card YAML to show the predicted PV for the next 5 days. This card combination uses the following:

- config-template-card (To enable javascript variables)
- bar-card (To show the bar card graphics)
- card_mod (To manipulate the CSS in the bar card)
- Predbat (For the PV solar prediction entities)


```
- type: custom:config-template-card
  variables:
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
        max: 35
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
          style: |-
            {% set energy_raw = states('sensor.predbat_pv_today') | float -%}
            #states {
              padding: 0px;
              box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
              {%- if energy_raw < 15 -%}
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
              --height: calc(100% - 3px) !important;
              --background-color: black;
              {% set energy = (((states('sensor.predbat_pv_today') | float) / 35) + 0.08) * 100 -%}              
                opacity: {{ energy }}%; 
              {%- if energy_raw < 15 -%}
                --bar-color: #ff3333 !important;
              {%- endif %}                
            }
            bar-card-contentbar {
              border-radius: 7px !important;
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;                   
              --background-color: black;
            }
            bar-card-backgroundbar {
              border-radius: 7px !important;            
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;   
              --background-color: black;
              --opacity: 100%;
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
              --font-weight: bold;
              color: black;
              {%- if energy_raw < 15 -%}
                color: color-mix(in srgb, #FF3333 60%, black);
              {%- endif %}                
              text-shadow: 0px 1px 2px white;
              font-size: 14px;
            }   
            bar-card-markerbar {
              background-color: white;
              filter: brightness(1.0);  
              width: calc(100% - 6px) !important;        
              margin-left: 3px;
              --border-radius: 7px;
              --margin-top: 3px;
              height: 2px !important;
            }
            bar-card-targetbar {
              --filter: brightness(0.39);
              --opacity: 0%;
              --background-color: black;
              width: calc(100% - 6px) !important;                        
              --border-radius: 7px;
              margin-left: 3px;
              --height: calc(100% - 6px) !important;
              --margin-top: 3px;
            }
      - type: custom:bar-card
        max: 35
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
            {% set energy_raw = states('sensor.predbat_pv_tomorrow') | float
            -%}            

            #states {
              padding: 0px;
              box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
              {%- if energy_raw < 15 -%}
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
              --height: calc(100% - 3px) !important;
              --background-color: black;
              {% set energy = (((states('sensor.predbat_pv_tomorrow') | float) / 35) + 0.08) * 100 -%}
                opacity: {{ energy }}%; 
              {%- if energy_raw < 15 -%}
                --bar-color: #ff3333 !important;
              {%- endif %}                 
            }

            bar-card-contentbar {
              border-radius: 7px !important;
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;                   
              --background-color: black;
            }

            bar-card-backgroundbar {
              border-radius: 7px !important;            
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;   
              --background-color: black;
              --opacity: 100%;
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
              --font-weight: bold;
              color: black;
              {%- if energy_raw < 15 -%}
                color: color-mix(in srgb, #FF3333 60%, black);
              {%- endif %}                
              text-shadow: 0px 1px 2px white;
              font-size: 14px;
            }   

            bar-card-markerbar {
              background-color: white;
              filter: brightness(1.0);  
              width: calc(100% - 6px) !important;        
              margin-left: 3px;
              --border-radius: 7px;
              --margin-top: 3px;
              height: 2px !important;
            }

            bar-card-targetbar {
              --filter: brightness(0.39);
              --opacity: 0%;
              --background-color: black;
              width: calc(100% - 6px) !important;                        
              --border-radius: 7px;
              margin-left: 3px;
              --height: calc(100% - 6px) !important;
              --margin-top: 3px;
            }
      - type: custom:bar-card
        max: 35
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
          style: |-
            {% set energy_raw = states('sensor.predbat_pv_d2') | float -%}
            #states {              
              padding: 0px;
              box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
              {%- if energy_raw < 15 -%}
                box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
              {%- endif %}                
              border-radius: 10px !important;
            }
            bar-card-currentbar {
              border-radius: 7px !important;
              width: calc(100% - 6px) !important;
              margin-left: 3px; 
              margin-top: 3px;
              height: calc(100% - 6px) !important;
              --height: calc(100% - 3px) !important;
              --background-color: black;
              {% set energy = (((states('sensor.predbat_pv_d2') | float) / 35) + 0.08) * 100 -%}
                opacity: {{ energy }}%; 
              {%- if energy_raw < 15 -%}
                --bar-color: #ff3333 !important;
              {%- endif %}                
            }
            bar-card-contentbar {
              border-radius: 7px !important;
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;                   
              --background-color: black;            
            }
            bar-card-backgroundbar {
              border-radius: 7px !important;            
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;   
              --background-color: black;
              --opacity: 100%;              
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
              --font-weight: bold;
              color: black;
              {%- if energy_raw < 15 -%}
                color: color-mix(in srgb, #FF3333 60%, black);
              {%- endif %}                
              text-shadow: 0px 1px 2px white;
              font-size: 14px;
            }   
            bar-card-markerbar {
              background-color: white;
              filter: brightness(1.0);  
              width: calc(100% - 6px) !important;        
              margin-left: 3px;
              --border-radius: 7px;
              --margin-top: 3px;
              height: 2px !important;
            }
            bar-card-targetbar {
              --filter: brightness(0.39);
              --opacity: 0%;
              --background-color: black;
              width: calc(100% - 6px) !important;                        
              --border-radius: 7px;
              margin-left: 3px;
              --height: calc(100% - 6px) !important;
              --margin-top: 3px;
            }
      - type: custom:bar-card
        max: 35
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
            {% set energy_raw = states('sensor.predbat_pv_d3') | float
            -%}        

            #states {
              padding: 0px;
              box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);                
              {%- if energy_raw < 15 -%}
                box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
              {%- endif %}               
              border-radius: 10px !important;
            }

            bar-card-currentbar {
              border-radius: 7px !important;
              width: calc(100% - 6px) !important;
              margin-left: 3px;          
              margin-top: 3px;
              height: calc(100% - 6px) !important;
              --height: calc(100% - 3px) !important;
              --background-color: black;
              {% set energy = (((states('sensor.predbat_pv_d3') | float) / 35) + 0.08) * 100 -%}
                opacity: {{ energy }}%; 
              {%- if energy_raw < 15 -%}
                --bar-color: #ff3333 !important;
              {%- endif %} 
            }

            bar-card-contentbar {
              border-radius: 7px !important;
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;                   
              --background-color: black;            
            }

            bar-card-backgroundbar {
              border-radius: 7px !important;            
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;   
              --background-color: black;
              --opacity: 100%;              
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
              --font-weight: bold;
              color: black;
              {%- if energy_raw < 15 -%}
                color: color-mix(in srgb, #FF3333 60%, black);
              {%- endif %}                
              text-shadow: 0px 1px 2px white;
              font-size: 14px;
            }   

            bar-card-markerbar {
              background-color: white;
              filter: brightness(1.0);  
              width: calc(100% - 6px) !important;        
              margin-left: 3px;
              --border-radius: 7px;
              --margin-top: 3px;
              height: 2px !important;
            }

            bar-card-targetbar {
              --filter: brightness(0.39);
              --opacity: 0%;
              --background-color: black;
              width: calc(100% - 6px) !important;                        
              --border-radius: 7px;
              margin-left: 3px;
              --height: calc(100% - 6px) !important;
              --margin-top: 3px;
            }
      - type: custom:bar-card
        max: 35
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
            {% set energy_raw = states('sensor.predbat_pv_d4') | float
            -%}          

            #states {
              padding: 0px;
              box-shadow: inset 0 0 0 3px color-mix(in srgb, var(--energy-solar-color) 80%, black);  
              {%- if energy_raw < 15 -%}
                box-shadow: inset 0 0 0 3px color-mix(in srgb, #ff3333 80%, black);
              {%- endif %}               
              border-radius: 10px !important;
            }

            bar-card-currentbar {
              border-radius: 7px !important;
              width: calc(100% - 6px) !important;
              margin-left: 3px;          
              margin-top: 3px;
              height: calc(100% - 6px) !important;
              --height: calc(100% - 3px) !important;
              --background-color: black;
              {% set energy = (((states('sensor.predbat_pv_d4') | float) / 35) + 0.08) * 100 -%}
                opacity: {{ energy }}%; 
              {%- if energy_raw < 15 -%}
                --bar-color: #ff3333 !important;
              {%- endif %}               
            }

            bar-card-contentbar {
              border-radius: 7px !important;
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;                   
              --background-color: black;            
            }

            bar-card-backgroundbar {
              border-radius: 7px !important;            
              margin-left: 3px;
              width: calc(100% - 6px) !important;
              height: calc(100% - 6px) !important;
              margin-top: 3px;   
              --background-color: black;
              --opacity: 100%;              
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
              --font-weight: bold;             
              color: black;
              {%- if energy_raw < 15 -%}
                color: color-mix(in srgb, #FF3333 60%, black);
              {%- endif %}                
              text-shadow: 0px 1px 2px white;
              font-size: 14px;
            }   

            bar-card-markerbar {
              background-color: white;
              filter: brightness(1.0);  
              width: calc(100% - 6px) !important;        
              margin-left: 3px;
              --border-radius: 7px;
              --margin-top: 3px;
              height: 2px !important;
            }

            bar-card-targetbar {
              --filter: brightness(0.39);
              --opacity: 0%;
              --background-color: black;
              width: calc(100% - 6px) !important;                        
              --border-radius: 7px;
              margin-left: 3px;
              --height: calc(100% - 6px) !important;
              --margin-top: 3px;
            }
```
