# Design-Patterns-for-IoT-Embedded-Systems

## Project Overview
This project implements a platform based on Design Patterns for IoT systems. The platform integrates devices (such as Raspberry Pi and ESP8266) to read temperature data and display it using Streamlit, providing real-time data visualization and interaction. The code structure utilizes Factory Method, Builder, and Observer patterns to create, manage, and monitor IoT devices efficiently.

## Features
- **Device Creation**: Devices are instantiated using Factory and Builder patterns.
- **Real-Time Data Visualization**: The platform reads temperature data from an ESP8266 via serial communication and displays it in real-time.
- **Dynamic UI with Streamlit**: A user-friendly interface with features for managing device associations and notifications.
- **Custom Styling**: Integrated support for loading custom CSS for UI enhancements.

## Project Structure
```
project-root/
|-- src/
|   |-- main.py
|   |-- observer.py
|   |-- devices.py
|   |-- factories.py
|   |-- builders.py
|-- app/
|   |-- broker.py
|-- data/
|   |-- Ambiente_Controlado.xlsx
|-- styles/
|   |-- styles.css
|-- assets/
|   |-- image.png
|-- README.md
```

## How to Run the Project
1. **Clone the repository** and navigate to the project directory.
2. **Install the required packages**:
   ```bash
   pip install streamlit
   ```
3. **Run the application**:
   ```bash
   streamlit run app/broker.py
   ```
4. **View the application** in your browser at `http://localhost:8501`.

## Main Components

### 1. `main.py`
Responsible for the core backend logic, including:
- Reading data from the Excel file.
- Creating devices based on the read data.
- Handling serial communication for reading temperature data from an ESP8266.

### 2. `broker.py`
Implements the Streamlit interface:
- Displays the main UI for managing and viewing device associations.
- Allows users to create new associations and view notifications from observers.
- Ensures the real-time update of data and handles background tasks with threads.

## Key Code Sections
### Initializing Devices and Threads
```python
if 'dispositivos_criados' not in st.session_state:
    dispositivos_criados = processar_e_criar_dispositivos()
    st.session_state['dispositivos_criados'] = dispositivos_criados
else:
    dispositivos_criados = st.session_state['dispositivos_criados']

if 'sensor_thread' not in st.session_state:
    stop_event = Event()
    sensor_thread = Thread(target=ler_sensor, args=(dispositivos_criados, stop_event), daemon=True)
    sensor_thread.start()
    st.session_state['sensor_thread'] = sensor_thread
    st.session_state['stop_event'] = stop_event
```

### Creating New Associations
```python
def adicionar_associacao():
    dispositivo_tag = dispositivo_selecionado
    subscriber_name_input = subscriber_name.strip()

    if not subscriber_name_input:
        st.warning("Por favor, insira o nome do objeto associado.")
        return

    dispositivo = next((d for d in dispositivos_criados if d.tag == dispositivo_tag), None)

    if dispositivo and isinstance(dispositivo, AIDevicePublisher):
        observer = GenericSubscriber(subscriber_name_input)
        dispositivo.attach(observer)
        associacoes.append({
            'dispositivo': dispositivo_tag,
            'subscriber': subscriber_name_input,
            'observer': observer
        })
        st.session_state['associacoes'] = associacoes.copy()
        st.success(f"Associação criada entre {dispositivo_tag} e {subscriber_name_input}")
    else:
        st.error("Dispositivo não encontrado ou inválido.")
```

## Custom Styling
To enhance the UI, the project includes a custom CSS file:
- **Path**: `styles/styles.css`
- **Usage**: The CSS is loaded into Streamlit using:
  ```python
  def load_css():
      css_path = os.path.join(styles_dir, 'styles.css')
      with open(css_path, 'r', encoding='utf-8') as f:
          st.markdown(f"<style>{f.read()}</style>", unsafe_allow_html=True)
  ```

## License
This project is licensed under the MIT License.

## Author
**Renato Ribas**  
Date: 01/11/2024

