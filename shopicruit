import json, urllib.request

TAX_RATE = 0.13
MASS_LIMIT = 100 #in kilograms
URL = "http://shopicruit.myshopify.com/products.json"

def option_counter(variant):
    '''
    Takes in an iterable and returns
    the number of options available
    for that variant.
    '''
    counter = 0
    for idx in range(1, 4):
        if variant["option" + str(idx)]:
            counter += 1
    return counter

def extractor(products, product_types):
    '''
    Parses through the all the products
    from the JSON file and forms a list
    containing only the items that are
    contained in the product_types
    iterable.
    Returns a list of products sorted in
    decreasing order of firstly mass and
    then price (in the case that two items
    of different prices have the same mass,
    preference will be given to the item
    with the lower cost).
    '''
    output = []
    for item in products:
        if item["product_type"] in product_types:
            variants = item["variants"]
            for variant in variants:
                taxable = 1 if variant["taxable"] else 0

                options = option_counter(variant)
                for option in range(options):
                    output.append((variant["grams"],
                                   float(variant["price"])*TAX_RATE*taxable,
                                   float(variant["price"]),
                                   variant["id"]))
    return sorted(output, reverse = True)

def packer(products, limit):
    '''
    Takes in a list of products that
    are sorted in descending order of
    mass. The products input is a list
    that is treated like a stack.
    The input limit is the maximum
    mass that can be carried.
    Returns the total mass of items
    selected, the total tax spent on
    purchases, total pretax expenditure,
    and a list containing the ids of
    the items to be purchased.
    '''
        cost_pretax, tax, mass = 0, 0, 0
        purchased = set()                   #the items with the least mass are at
        while products and mass <= limit:   #the 'top of the stack'. Keep purchasing
            product = products.pop()        #the lightest available item and
            mass += product[0]              #pop it from the stack of available
            tax += product[1]               #items. Keep track of the increase
            cost_pretax += product[2]       #in mass and cost of the items.
            purchased.add(product)          
        if mass > limit:                    #in case the last item purchased overflows
            mass -= product[0]              #our maximal mass limit, remove the last
            tax -= product[1]               #item that was added.
            cost_pretax -= product[2]
            purchased.remove(product)
        tax = round(tax, 2)
        cost_pretax = round(cost_pretax, 2)
        return (mass, tax, cost_pretax, purchased)

def presentation_helper(product):
    '''
    Helper function used to
    print to the console.
    '''
    return "\tThe item with id {0}.".format(product[-1])

def presentation(solution):
    '''
    Function that prints the final
    result to the console.
    '''
    separator = ".--. .-.. . .- ... . .... .. .-. . -- ."

    print("\n", separator, "\n")
    print("Items purchased:")
    for item in solution[3]:
        print(presentation_helper(item))

    print("\n", separator)
    print("\nTotal cost of all purchases, before tax:")
    print("$\t{0}.".format(solution[2]))

    print(separator)
    print("\nTotal tax on all (taxable) purchases at {0}% average tax rate:".format(100 * TAX_RATE))
    print("$\t{0}.".format(solution[1]))

    print(separator)
    print("\nTotal cost of all purchases, after applicable taxes:".format(100 * TAX_RATE))
    print("$\t{0}.".format(round(solution[1] + solution[2], 2)))

    print(separator)
    print("\nTotal mass of purchases:")
    print("KG\t{0}.".format(solution[0]/1000))
    print(separator, "\n")

def run(all_products, mass_limit, desired_items):
    '''
    Wrapper function, prints the
    desired answer to the console.
    '''
    mass_limit *= 1000                                  #convert to grams
    products = extractor(all_products, desired_items)   #parse through all products and find the desired ones
    solution = packer(products, mass_limit)             #purchase as many items without exceeding mass_limit
    presentation(solution)                              #print the answers to the console

response = urllib.request.urlopen(URL).read()
all_products = json.loads(response.decode('utf-8'))["products"]

run(all_products, MASS_LIMIT, {"Computer", "Keyboard"}) #solve the given problem
