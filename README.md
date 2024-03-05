# python
Problem with solving: Not enough elements in the line
import csv
import matplotlib.pyplot as plt

def read_income_shares(file_name, wealthiest_file_name):
    income_shares = {}
    countries = []
    years = []  # Initialize the years list
    
    # Read data from the first file
    try:
        with open(file_name, 'r', encoding='utf-8') as file:
            reader = csv.reader(file)
            
            # Skip the first 4 rows
            for _ in range(4):
                next(reader)

            # Read the header to get the years
            header = next(reader)
            years = [int(year.strip('"')) for year in header[4:] if year.strip('"').isdigit()]  # Extract valid years

            for i, line in enumerate(reader, start=5):
                try:
                    country_name = line[0].strip('"')
                    
                    # Replace """" with 0, then remove double quotes and extract the value from each line
                    values = []
                    for val in line[4:]:
                        val = val.replace('""""', '0').replace('"', '').strip()
                        if val and val.replace('.', '').isdigit():
                            values.append(float(val))
                        else:
                            values.append(0)

                    income_shares.setdefault(country_name, {}).update({'Values': values})
                    if country_name not in countries:
                        countries.append(country_name)
                except Exception as e:
                    print(f"Error in line {i}: {e}")
                    print(f"Line content: {line}")
    except FileNotFoundError:
        print(f"Error: File '{file_name}' not found.")
    except Exception as e:
        print(f"Error: An unexpected error occurred: {e}")
    
    # Read data from the second file
    try:
        with open(wealthiest_file_name, 'r', encoding='utf-8') as file:
            reader = csv.reader(file)
            
            # Skip the first 4 rows
            for _ in range(4):
                next(reader)

            for i, line in enumerate(reader, start=5):
                try:
                    country_name = line[0].strip('"')
                    
                    # Replace """" with 0, then remove double quotes and extract the value from each line
                    values = []
                    for val in line[4:]:
                        val = val.replace('""""', '0').replace('"', '').strip()
                        if val and val.replace('.', '').isdigit():
                            values.append(float(val))
                        else:
                            values.append(0)

                    # Update the existing data or add new data for the country
                    if country_name in income_shares:
                        income_shares[country_name].setdefault('Values_Wealthiest', []).extend(values)
                    else:
                        income_shares.setdefault(country_name, {}).update({'Values_Wealthiest': values})
                        if country_name not in countries:
                            countries.append(country_name)
                except Exception as e:
                    print(f"Error in line {i}: {e}")
                    print(f"Line content: {line}")
    except FileNotFoundError:
        print(f"Error: File '{wealthiest_file_name}' not found.")
    except Exception as e:
        print(f"Error: An unexpected error occurred: {e}")

    return income_shares, countries, years

def plot_income_distribution(countries):
    income_data, _, _ = read_income_shares('C:\\Users\\Fabian\\Desktop\\Python Ausarbeitung\\Bravo\\one.txt', 'C:\\Users\\Fabian\\Desktop\\Python Ausarbeitung\\Bravo\\two.txt')
    
    formatted_countries = []  # Collect formatted country names
    
    for country in countries:
        # Bereinigung des Ländernamens
        country_formatted = country.strip('" \ufeff')
        formatted_countries.append(country_formatted)  # Collect formatted country names
        
        # Überprüfen, ob Daten für das Land vorhanden sind
        if country_formatted in income_data:
            income_data_country = income_data[country_formatted]['Values']
            income_data_wealthiest = income_data[country_formatted].get('Values_Wealthiest', [])
            
            # Nur die Jahre von 1960 bis 2022 auswählen
            years_to_plot = list(range(1960, 2023))
            
            # Convert values to percentage
            income_data_country_percent = [val * 100 for val in income_data_country]
            income_data_wealthiest_percent = [val * 100 for val in income_data_wealthiest]
            
            # Filter out values equal to 0
            non_zero_years = [year for year, val in zip(years_to_plot, income_data_country_percent) if val > 0]
            non_zero_percentages = [val for val in income_data_country_percent if val > 0]
            non_zero_percentages_wealthiest = [val for val in income_data_wealthiest_percent if val > 0]
            
            # Print the data for debugging
            print(f"Years for {country_formatted}: {non_zero_years}")
            print(f"Total Percentages for {country_formatted}: {non_zero_percentages}")
            print(f"Wealthiest Percentages for {country_formatted}: {non_zero_percentages_wealthiest}")
            
            # Plot only if data is available for the year
            plt.plot(non_zero_years, non_zero_percentages, label=f'{country_formatted} - Total')
            plt.plot(non_zero_years, non_zero_percentages_wealthiest, label=f'{country_formatted} - Wealthiest 10%', linestyle='dashed')

    plt.title('Income Distribution Over Years')
    plt.xlabel('Year')
    plt.ylabel('Income Share (%)')
    plt.ylim(0, 100)  # Setzt die Y-Achse auf 0 bis 100 Prozent
    plt.axis([1960, 2022, 0, 100])
    plt.grid(True)
    
    # Nur Legende anzeigen, wenn Daten vorhanden sind
    if any(formatted_country in income_data for formatted_country in formatted_countries):
        plt.legend(loc='upper left', bbox_to_anchor=(1, 1))  # Move legend outside the plot area

    plt.savefig('income_distribution_plot.png', bbox_inches='tight')  # Das Diagramm als PNG-Datei speichern
    plt.show()

# Beispielaufruf
countries_to_plot = ['"Germany"']
plot_income_distribution(countries_to_plot)



3 Data Representation:
Write a function that represents the income share of the richest and poorest 10 percent of the population for a country or a list of countries. The representation period is from 1960 to 2022. Note: You can encapsulate a single country as a list if you use square brackets. Label your chart informatively and save it as a PNG file for use in your slides.

4 Fair Distribution:
Find out if there are countries where the share of income is nearly evenly distributed. That is, 10 percent of the population should also have a 10 percent share of income. This condition is challenging to precisely fulfill. Consider how much deviation upward/downward is acceptable.

5 Top-10 Inequality:
Calculate the average income share of the richest and poorest 10 percent in all countries. In which ten countries is the distribution particularly unfair? Which countries are represented in both Top-10 lists?


For now i am struggling with the issue "Not enough elements in the line"
