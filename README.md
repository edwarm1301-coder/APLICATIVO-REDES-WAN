# 🌐 APLICATIVO-REDES-WAN 🌐
🎛️ APLICATIVO REDES WAN BRAYAN IVAN RIBON QUINTERO-EDWAR ROBERTO MARTINEZ PINTO- JEIMY LORENA NIÑO CORREA
### PROMPT
Contexto del Proyecto:
Actúa como un Ingeniero de DevOps y Telecomunicaciones experto en desarrollo de software con Python. Debes construir una aplicación web corporativa/académica utilizando Streamlit orientada a la simulación y generación dinámica de scripts de configuración de red para estudiantes de la UCompensar. El diseño visual de la interfaz debe ser moderno y limpio, utilizando estilos avanzados de CSS embebido con una estética de modo oscuro (Glassmorphism y tonos azulados/púrpuras).

Requerimientos de Arquitectura de Red y Lógica de Datos:

Cálculo de Direccionamiento Automático: Utiliza la librería integrada ipaddress de Python para que, a partir de una dirección IP de Gateway (por defecto 172.16.10.1) y una máscara en formato CIDR seleccionadas por el usuario, el sistema calcule matemáticamente la dirección de red base y su respectiva máscara tradicional en formato decimal, así como su wildcard.

Parámetros Globales e Inputs Dinámicos: El panel lateral (st.sidebar) debe capturar las variables clave de la infraestructura: Dirección IP, Selector de Máscara CIDR (rango 24 a 30), ID de la VLAN (numérico), Nombre de la VLAN (texto) y un Deslizador (st.slider) interactivo para definir la cantidad de equipos en la topología física en un rango de 1 a 10 equipos.

Catálogo de Referencias de Hardware: Añade un menú desplegable (st.selectbox) que ofrezca al usuario una lista de modelos y referencias reales del mercado de telecomunicaciones. Esta lista debe cambiar de manera dinámica dependiendo del Vendor tecnológico seleccionado por el usuario en la interfaz:

Si el Vendor es CISCO: Mostrar modelos como ISR 4331, Catalyst 9300, ASR 1001-X, Nexus 9300.

Si el Vendor es HUAWEI: Mostrar modelos como NetEngine AR6140, CloudEngine S5735-L, NetEngine 8000 M1A, CloudEngine S6730-H.

Estructura de la Interfaz Central (Pestañas de Protocolos):
El cuerpo principal de la página debe llevar el título institucional destacado: "CONFIGURACION DE PROTOCOLOS BGP,ISIS O OSPF UCOMPENSAR". Debe dividirse obligatoriamente en tres pestañas independientes (st.tabs) para los protocolos principales de enrutamiento:

Pestaña OSPF (Link-State): Debe mostrar una tarjeta teórica explicativa del protocolo y una caja de terminal oscura con comandos reales de sintaxis IOS para Cisco o VRP para Huawei (dependiendo del radio button activo), inyectando dinámicamente las variables calculadas (VLAN, IP, Máscara, Wildcard, etc.).

Pestaña BGP (Path-Vector): Debe simular la configuración de vecindades BGP calculando lógicamente una IP adyacente para el comando neighbor o peer.

Pestaña IS-IS (ISO CLNS): Debe incluir una característica exclusiva: un selector de radio (st.radio) que permita elegir la naturaleza de los "Sistemas Intermedios", alternando entre Routers de Núcleo (Core) o Switches Multicapa (Capa 3).

Requerimiento Gráfico Avanzado (Topología Dinámica en SVG):
Al final de la página, embebe un componente HTML que renderice un mapa de topología física autogenerado en formato SVG que responda en tiempo real a las interacciones del usuario:

Debe graficar exactamente la cantidad de nodos configurados en el slider (hasta 10).

Los enlaces deben simular cables de fibra óptica con un haz láser animado que fluye continuamente a lo largo de la red (stroke-dashoffset).

Lógica de color adaptativa: El color de los cables láser y los bordes de los equipos debe cambiar dinámicamente según la pestaña del protocolo que se esté visualizando: Celeste (#38bdf8) para OSPF, Amarillo/Ámbar (#fbbf24) para BGP y Púrpura (#a855f7) para IS-IS.

Lógica de figuras adaptativa: Si en la pestaña IS-IS el usuario selecciona "Switches Multicapa", los círculos clásicos de los routers deben transformarse en cajas cuadradas con el icono tradicional de conmutación de un Switch de capa 3 ([X]), renombrando las etiquetas inferiores automáticamente (ej. SW_L3_1).

🏷️CODIGO UTLIZADO DESDE VS CODE CON EL LENGUAJE DE PROGRAMACIÓN DE PYTHON

import streamlit as st
import ipaddress

# 1. CONFIGURACIÓN DE LA PÁGINA (Pestaña del navegador)
st.set_page_config(
    page_title="CONFIGURACION DE PROTOCOLOS BGP,ISIS Y OSPF UCOMPENSAR",
    page_icon="⚡",
    layout="wide"
)

# Estilos visuales optimizados (Tema Oscuro Premium Estilo Glassmorphism)
st.markdown("""
    <style>
    .stApp { background-color: #0f172a; color: #f8fafc; }
    .terminal-box {
        background-color: #050505;
        color: #38bdf8;
        font-family: 'Fira Code', monospace;
        padding: 20px;
        border-radius: 12px;
        border-left: 5px solid #38bdf8;
        white-space: pre-wrap;
        box-shadow: inset 0px 0px 15px rgba(0,0,0,0.9);
    }
    .protocol-card {
        background: rgba(30, 41, 59, 0.6);
        padding: 20px;
        border-radius: 14px;
        border: 1px solid rgba(56, 189, 248, 0.2);
        margin-bottom: 20px;
    }
    .network-title {
        font-family: 'Inter', sans-serif;
        font-weight: 800;
        background: linear-gradient(90deg, #38bdf8, #34d399);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        margin-top: 0px;
        margin-bottom: 5px;
        font-size: 2.5rem;
    }
    </style>
""", unsafe_allow_html=True)

# --- LÓGICA DE DIRECCIONAMIENTO DE RED ---
def calcular_infraestructura(ip_str, cidr_val):
    try:
        interface = ipaddress.IPv4Interface(f"{ip_str}/{cidr_val}")
        network_calculated = interface.network
        netmask_calculated = interface.netmask
        wildcard_calculated = interface.hostmask
        return str(network_calculated.network_address), str(netmask_calculated), str(wildcard_calculated)
    except Exception:
        return "IP Inválida", "255.255.255.0", "0.0.0.255"

# --- PANEL LATERAL: ENTRADA DE DATOS ---
with st.sidebar:
    st.markdown("<h2 style='color: #38bdf8; margin-bottom:0;'>⚙️ Parámetros Globales</h2>", unsafe_allow_html=True)
    st.write("Configuración de infraestructura académica.")
    st.markdown("---")
    
    ip_gateway = st.text_input("📍 Dirección IP (Host/Gateway):", value="172.16.10.1")
    cidr = st.selectbox("🎛️ Máscara de Red (CIDR):", options=[24, 25, 26, 27, 28, 29, 30], index=0)
    
    st.markdown("---")
    vlan_id = st.number_input("🆔 ID de la VLAN:", min_value=1, max_value=4094, value=100)
    vlan_name = st.text_input("📛 Nombre de la VLAN:", value="VLAN_PRODUCCION")
    
    st.markdown("---")
    # Aumentado el límite de equipos de 5 a 10
    num_equipos = st.slider("🌐 Equipos en Topología Física:", min_value=1, max_value=10, value=4)

# Procesamiento dinámico de red
ip_red, mascara, wildcard = calcular_infraestructura(ip_gateway, cidr)

# --- PANEL CENTRAL: ENCABEZADO INSTITUCIONAL ---
st.markdown("<h1 class='network-title'>CONFIGURACION DE PROTOCOLOS BGP,ISIS O OSPF UCOMPENSAR</h1>", unsafe_allow_html=True)
st.markdown(f"<p style='color: #94a3b8; letter-spacing: 2px; font-size:12px; font-weight:bold;'>MATRÍCULA DE RED ACTIVA: <span style='color:#34d399; font-family:monospace;'>{ip_red}/{cidr}</span></p>", unsafe_allow_html=True)
st.markdown("---")

# Selector de tecnología de Vendor
vendor_selected = st.radio("🛠️ Selecciona el Sistema Operativo del Vendor:", ["CISCO (IOS / IOS-XE)", "HUAWEI (VRP)"], horizontal=True)

# Catálogo dinámico de referencias según el Vendor seleccionado
st.markdown("### 🏷️ Referencia del Hardware Seleccionado")
if "CISCO" in vendor_selected:
    referencia_equipo = st.selectbox(
        "Elige el modelo del dispositivo Cisco para la simulación:",
        options=[
            "ISR 4331 (Router de Servicios Integrados Empresarial)",
            "Catalyst 9300 (Switch Modular de Capa 3 - Core/Distribución)",
            "ASR 1001-X (Router de Agregación de Servicios de Borde)",
            "Nexus 9300 (Switch de Alta Densidad para Data Center)"
        ]
    )
else:
    referencia_equipo = st.selectbox(
        "Elige el modelo del dispositivo Huawei para la simulación:",
        options=[
            "NetEngine AR6140 (Router Empresarial de Próxima Generación)",
            "CloudEngine S5735-L (Switch de Capa 3 Esencial)",
            "NetEngine 8000 M1A (Router de Borde de Alta Capacidad para ISP)",
            "CloudEngine S6730-H (Switch Core de Alta Densidad 10GE)"
        ]
    )
st.markdown("---")

# Crear pestañas de protocolos
tab_ospf, tab_bgp, tab_isis = st.tabs(["📡 OSPF (Link-State)", "🌐 BGP (Path-Vector)", "🌌 IS-IS (ISO CLNS)"])

# Variables dinámicas de control para la topología física
color_topologia = "#38bdf8" 
tipo_dispositivo_isis = "Router" # Por defecto son Routers

# --- CONTENIDO OSPF ---
with tab_ospf:
    color_topologia = "#38bdf8" # Celeste
    st.markdown(f"""
    <div class="protocol-card">
        <h4><b>Protocolo OSPF (Open Shortest Path First)</b></h4>
        <p>Protocolo de enrutamiento interno (IGP) de tipo <b>Estado de Enlace</b>. Utiliza el algoritmo de Dijkstra para calcular la ruta más corta libre de bucles. Su métrica nativa es el <b>Costo</b> (derivado del ancho de banda de la interfaz). Es ideal para redes jerárquicas empresariales basadas en áreas.</p>
        <p><small>📟 Hardware simulado: <b>{referencia_equipo}</b></small></p>
    </div>
    """, unsafe_allow_html=True)
    
    if "CISCO" in vendor_selected:
        config_lines = (
            f"! === CONFIGURACIÓN OSPF EN CISCO IOS ===\n"
            f"! Modelo de Equipo: {referencia_equipo}\n"
            f"vlan {vlan_id}\n"
            f" name {vlan_name}\n"
            f"!\n"
            f"interface GigabitEthernet0/1\n"
            f" description Enlace_Trunk_Hacia_LAN\n"
            f" no shutdown\n"
            f"!\n"
            f"interface GigabitEthernet0/1.{vlan_id}\n"
            f" description Subinterfaz Gateway VLAN {vlan_name}\n"
            f" encapsulation dot1Q {vlan_id}\n"
            f" ip address {ip_gateway} {mascara}\n"
            f"!\n"
            f"router ospf 1\n"
            f" router-id 1.1.1.1\n"
            f" network {ip_red} {wildcard} area 0\n"
        )
    else:
        config_lines = (
            f"# === CONFIGURACIÓN OSPF EN HUAWEI VRP ===\n"
            f"# Modelo de Equipo: {referencia_equipo}\n"
            f"vlan {vlan_id}\n"
            f" description {vlan_name}\n"
            f"#\n"
            f"interface GigabitEthernet0/0/1\n"
            f" undo shutdown\n"
            f"#\n"
            f"interface GigabitEthernet0/0/1.{vlan_id}\n"
            f" description Subinterfaz Gateway VLAN {vlan_name}\n"
            f" dot1q termination vid {vlan_id}\n"
            f" ip address {ip_gateway} {cidr}\n"
            f" arp broadcast enable\n"
            f"#\n"
            f"ospf 1 router-id 1.1.1.1\n"
            f" area 0.0.0.0\n"
            f"  network {ip_red} {wildcard}\n"
        )
    st.markdown(f"<div class='terminal-box'>{config_lines}</div>", unsafe_allow_html=True)

# --- CONTENIDO BGP ---
with tab_bgp:
    color_topologia = "#fbbf24" # Ámbar / Amarillo de tu HTML
    st.markdown(f"""
    <div class="protocol-card">
        <h4><b>Protocolo BGP (Border Gateway Protocol)</b></h4>
        <p>Protocolo de enrutamiento de tipo <b>Vector de Ruta</b> que hace funcionar a Internet. A diferencia de OSPF, no se basa en velocidades de cables, sino en <b>Políticas y Atributos</b> (como AS-Path, Local Preference y MED). Se organiza mediante Sistemas Autónomos (AS) y es altamente escalable.</p>
        <p><small>📟 Hardware simulado: <b>{referencia_equipo}</b></small></p>
    </div>
    """, unsafe_allow_html=True)
    
    parts = ip_gateway.split('.')
    neighbor_ip = f"{parts[0]}.{parts[1]}.{parts[2]}.2" if len(parts) == 4 else "X.X.X.X"
    
    if "CISCO" in vendor_selected:
        config_lines = (
            f"! === CONFIGURACIÓN BGP EN CISCO IOS ===\n"
            f"! Modelo de Equipo: {referencia_equipo}\n"
            f"vlan {vlan_id}\n"
            f" name {vlan_name}\n"
            f"!\n"
            f"interface GigabitEthernet0/1.{vlan_id}\n"
            f" encapsulation dot1Q {vlan_id}\n"
            f" ip address {ip_gateway} {mascara}\n"
            f"!\n"
            f"router bgp 65001\n"
            f" bgp router-id 1.1.1.1\n"
            f" neighbor {neighbor_ip} remote-as 65001\n"
            f" neighbor {neighbor_ip} description Vecino_BGP_Interno\n"
            f" address-family ipv4 unicast\n"
            f"  network {ip_red} mask {mascara}\n"
            f"  neighbor {neighbor_ip} activate\n"
        )
    else:
        config_lines = (
            f"# === CONFIGURACIÓN BGP EN HUAWEI VRP ===\n"
            f"# Modelo de Equipo: {referencia_equipo}\n"
            f"vlan {vlan_id}\n"
            f" description {vlan_name}\n"
            f"#\n"
            f"interface GigabitEthernet0/0/1.{vlan_id}\n"
            f" dot1q termination vid {vlan_id}\n"
            f" ip address {ip_gateway} {cidr}\n"
            f" arp broadcast enable\n"
            f"#\n"
            f"bgp 65001\n"
            f" router-id 1.1.1.1\n"
            f" peer {neighbor_ip} as-number 65001\n"
            f" #\n"
            f" ipv4-family unicast\n"
            f"  network {ip_red} {mascara}\n"
            f"  peer {neighbor_ip} enable\n"
        )
    st.markdown(f"<div class='terminal-box'>{config_lines}</div>", unsafe_allow_html=True)

# --- CONTENIDO IS-IS ---
with tab_isis:
    color_topologia = "#a855f7" # Púrpura de tu HTML
    
    st.markdown(f"""
    <div class="protocol-card">
        <h4><b>Protocolo IS-IS (Intermediate System to Intermediate System)</b></h4>
        <p>Protocolo de enrutamiento de <b>Estado de Enlace</b> nativo de la capa de enlace (Capa 2 ISO CLNS). Altamente preferido por los proveedores de servicios de internet (ISPs) debido a su extrema estabilidad y su capacidad nativa para transportar tráfico IPv4 e IPv6 simultáneamente sin alterar el núcleo del protocolo.</p>
        <p><small>📟 Hardware simulado: <b>{referencia_equipo}</b></small></p>
    </div>
    """, unsafe_allow_html=True)
    
    # OPCIÓN SOLICITADA: Cambiar la naturaleza física del equipo en IS-IS
    tipo_dispositivo_isis = st.radio(
        "🔀 Selecciona la naturaleza de los Sistemas Intermedios (IS) para la topología de este protocolo:",
        ["Routers de Núcleo (Core)", "Switches Multicapa (Capa 3)"],
        horizontal=True
    )
    
    # Asignar etiqueta para los nodos del SVG interactivo
    label_prefijo = "Core_R" if "Routers" in tipo_dispositivo_isis else "Switch_L3_"
    
    if "CISCO" in vendor_selected:
        config_lines = (
            f"! === CONFIGURACIÓN IS-IS EN CISCO IOS ===\n"
            f"! Modelo de Equipo: {referencia_equipo}\n"
            f"! Tipo de Nodo: {tipo_dispositivo_isis}\n"
            f"vlan {vlan_id}\n"
            f" name {vlan_name}\n"
            f"!\n"
            f"router isis CORE_ISP\n"
            f" net 49.0001.0000.0000.0001.00\n"
            f" is-type level-2-only\n"
            f"!\n"
            f"interface GigabitEthernet0/1.{vlan_id}\n"
            f" encapsulation dot1Q {vlan_id}\n"
            f" ip address {ip_gateway} {mascara}\n"
            f" ip router isis CORE_ISP\n"
        )
    else:
        config_lines = (
            f"# === CONFIGURACIÓN IS-IS EN HUAWEI VRP ===\n"
            f"# Modelo de Equipo: {referencia_equipo}\n"
            f"# Tipo de Nodo: {tipo_dispositivo_isis}\n"
            f"vlan {vlan_id}\n"
            f" description {vlan_name}\n"
            f"#\n"
            f"isis 1\n"
            f" network-entity 49.0001.0000.0000.0001.00\n"
            f" is-level level-2\n"
            f"#\n"
            f"interface GigabitEthernet0/0/1.{vlan_id}\n"
            f" dot1q termination vid {vlan_id}\n"
            f" ip address {ip_gateway} {cidr}\n"
            f" isis enable 1\n"
        )
    st.markdown(f"<div class='terminal-box'>{config_lines}</div>", unsafe_allow_html=True)


# ====================================================================================
# 🌌 MAPA DE TOPOLOGÍA FÍSICA DINÁMICA CON LÁSER ADAPTATIVO (HASTA 10 NODOS)
# ====================================================================================
st.write("##")
st.markdown("### 🗺️ Mapa de Topología Física y Flujo de Datos Inteligente")

anchura_lienzo = 1000  # Expandido para albergar cómodamente los 10 nodos
spacing = anchura_lienzo / (num_equipos + 1)
links_svg = ""
nodes_svg = ""

# Dibujar enlaces láser con el color exacto de la pestaña de protocolo activa
for i in range(num_equipos - 1):
    x1 = spacing * (i + 1)
    x2 = spacing * (i + 2)
    links_svg += f"""
    <line x1="{x1}" y1="65" x2="{x2}" y2="65" stroke="#1e293b" stroke-width="6" stroke-linecap="round"/>
    <line x1="{x1}" y1="65" x2="{x2}" y2="65" stroke="{color_topologia}" stroke-width="3" stroke-dasharray="8,8" stroke-linecap="round">
        <animate attributeName="stroke-dashoffset" values="50;0" dur="2s" repeatCount="indefinite" />
    </line>
    """

# Determinar prefijo genérico de nombres para OSPF/BGP
if "OSPF" in st.session_state.get("current_tab", "📡 OSPF (Link-State)"):
    label_prefijo = "Core_R"
elif "BGP" in st.session_state.get("current_tab", "📡 OSPF (Link-State)"):
    label_prefijo = "Borde_R"

# Dibujar Nodos estilizados (Routers redondos o Switches cuadrados)
for i in range(num_equipos):
    x = spacing * (i + 1)
    
    # Si estamos en IS-IS y se eligió "Switches", dibujamos un cuadrado, de lo contrario un círculo de Router
    if color_topologia == "#a855f7" and "Switches" in tipo_dispositivo_isis:
        # Icono de Switch (Cuadrado con flechas cruzadas)
        icono_grafico = f"""
        <rect x="{x-18}" y="47" width="36" height="36" rx="4" fill="#0f172a" stroke="{color_topologia}" stroke-width="3" />
        <path d="M{x-10} 57 L{x+10} 73 M{x+10} 57 L{x-10} 73 M{x-12} 65 L{x+12} 65" stroke="#f8fafc" stroke-width="1.5" stroke-linecap="round"/>
        """
        nombre_nodo = f"SW_L3_{i+1}"
    else:
        # Icono de Router (Círculo clásico con flechas cruzadas)
        icono_grafico = f"""
        <circle cx="{x}" cy="65" r="20" fill="#0f172a" stroke="{color_topologia}" stroke-width="3" />
        <path d="M{x-8} 65 L{x+8} 65 M{x} 57 L{x} 73 M{x-5} 60 L{x+5} 70 M{x-5} 70 L{x+5} 60" stroke="#f8fafc" stroke-width="1.5" stroke-linecap="round"/>
        """
        nombre_nodo = f"Core_R{i+1}"

    nodes_svg += f"""
    <g>
        <circle cx="{x}" cy="65" r="24" fill="rgba(6, 182, 212, 0.08)">
            <animate attributeName="r" values="22;26;22" dur="3s" repeatCount="indefinite"/>
        </circle>
        {icono_grafico}
        <rect x="{x-45}" y="100" width="90" height="24" rx="6" fill="#1e293b" stroke="#334155" stroke-width="1"/>
        <text x="{x}" y="116" fill="#e2e8f0" font-size="10" font-family="sans-serif" font-weight="bold" text-anchor="middle">{nombre_nodo}</text>
    </g>
    """

svg_code = f"""
<svg width="100%" height="140" viewBox="0 0 {anchura_lienzo} 140" style="background: radial-gradient(circle, #0b1329 0%, #020617 100%); border-radius:16px; border: 1px solid #1e293b;">
    <defs>
        <pattern id="grid" width="20" height="20" patternUnits="userSpaceOnUse">
            <path d="M 20 0 L 0 0 0 20" fill="none" stroke="rgba(255,255,255,0.02)" stroke-width="1"/>
        </pattern>
    </defs>
    <rect width="100%" height="100%" fill="url(#grid)" />
    {links_svg}
    {nodes_svg}
</svg>
"""
st.components.v1.html(svg_code, height=155)
