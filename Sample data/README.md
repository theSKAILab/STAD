# STAD Evaluation Sample Data

## Overview

Having developed and implemented the Spatial Temporal Aggregate Data (STAD) ontology, we evaluated its utility for representing and reasoning about spatial and temporal aggregations of data. STAD has been successfully deployed in the **Digital Forest Knowledge Graph (KG)**, demonstrating its applicability to a large-scale, real-world spatio-temporal dataset.

The Digital Forest KG contains over **1.3 billion triples** covering data from the state of Maine. It contains information about more than **146 tree species** and **26 environmental variables** at a spatial resolution of approximately **1 square kilometer**, corresponding to the size of Level 13 and Level 14 cells of the S2 reference grid described in [34]. This deployment demonstrates STAD's ability to support the construction of the Digital Forest KG and similar large-scale spatio-temporal knowledge graphs.

Because of the overall size and complexity of the Digital Forest KG, we use a smaller dataset to provide a focused evaluation of STAD. Specifically, we use temperature measurements collected from **2012 through 2022** at two sites of the **Soil Climate Analysis Network (SCAN)** in New Hampshire:

- **Hubbard Brook**
- **Mascoma River**

Using these data, we demonstrate how the seven competency questions defined in Section 1.2 (Q1-Q7) can be expressed as SPARQL queries and answered using a STAD-based knowledge graph.

## Evaluation Approach

The evaluation consists of the following steps:

1. **Compute aggregates from the raw SCAN data.**  
   Temperature observations from the two SCAN sites were processed to calculate a range of temporal and spatio-temporal aggregates.

2. **Instantiate STAD.**  
   Each computed aggregate was represented using STAD classes and properties. The representation captures not only the aggregate value but also its associated semantics, including its quantity, spatial support, temporal support, aggregation method, and source dataset.

3. **Create the knowledge graph.**  
   The resulting RDF data was stored together with the STAD ontology in a knowledge graph. The ontology provides the schema (TBox), while the instantiated aggregate data forms the knowledge graph's ABox.

4. **Evaluate the competency questions using SPARQL.**  
   The competency questions were translated into SPARQL queries and executed against the STAD-based knowledge graph. This evaluates whether STAD can adequately represent aggregate data and support meaningful querying and reasoning about its spatial and temporal semantics.

This process demonstrates how STAD enables the precise and unambiguous representation of aggregate data, supporting its sharing, querying, comparison, and reasoning while preserving the semantics of how the aggregates were produced.

## 5.1 Instantiating STAD from Real Raw Data

For each of the two SCAN sites, we computed the following aggregates from the hourly temperature observations:

1. Daily mean temperature
2. Daily minimum temperature
3. Daily maximum temperature
4. Annual mean temperature
5. Annual summer mean temperature
6. Annual winter mean temperature
7. 10-year annual mean temperature
8. 10-year annual summer mean temperature
9. 10-year annual winter mean temperature

In addition to these temporal aggregates, we computed **spatio-temporal aggregates** for each variable by combining measurements from the two SCAN sites within New Hampshire.

The resulting triplified data are available in the [`Sample data`](https://github.com/theSKAILab/STAD/tree/main/Sample%20data) directory of this repository.

Each computed aggregate is encoded as a small RDF graph consisting of a set of triples in **Turtle syntax**. These graphs describe the aggregate as an instance of STAD classes and properties. Collectively, the Turtle files constitute the **ABox** used in the evaluation.

## Representing Aggregate Semantics

STAD represents an aggregate as more than a numerical value. Each aggregate is described using RDF triples that explicitly capture the information needed to understand its meaning and provenance.

The representation captures several key aspects of an aggregate:

- **Quality kind** — the type of quality being represented, such as temperature.
- **Quantity** — the specific measured or calculated quantity associated with the aggregate.
- **Spatial support** — the geographic area represented by the aggregate.
- **Temporal support** — the time period represented by the aggregate.
- **Aggregation period** — the specific temporal periods included in the aggregation.
- **Statistical aggregation method** — the statistical operation used to calculate the aggregate.
- **Base dataset** — the dataset from which the aggregate was derived.
- **Dataset description** — information describing the spatial and temporal resolution of the source dataset.
- **Algorithm execution and parameters** — information about the specific execution of the aggregation algorithm and the parameters used.

For example, consider a summer mean temperature calculated for New Hampshire over the period **2013-2022** using measurements from the two SCAN sites. The STAD representation identifies the quantity as temperature, specifies New Hampshire as the spatial support, identifies the relevant summer periods between 2013 and 2022 as the temporal aggregation period, links the result to its source dataset, and records that the value was calculated using an arithmetic mean.

This makes the semantics of the aggregate explicit and machine-readable rather than leaving important details implicit.

## Temporal Support

Temporal support describes the period and temporal structure represented by an aggregate.

For the example of a summer mean temperature calculated over 2013-2022, the temporal coverage is the continuous interval spanning the calendar years **2013 through 2022**.

The aggregation period, `ex:SummerAggregate2013-2022`, is modeled as a temporal aggregate whose parts are instances of a custom `ex:Summer` class. Each instance of `ex:Summer` represents the summer period within an individual year, extending from **June 1 through August 31** and having a duration of approximately three months.

`ex:SummerAggregate2013-2022` is itself represented as a temporal part of the continuous 2013-2022 interval. This allows the representation to distinguish the complete temporal coverage from the specific recurring summer periods that contributed to the aggregate.

This distinction is important because an aggregate calculated from summer observations between 2013 and 2022 does not represent all observations continuously distributed throughout the entire ten-year interval. Instead, it represents the summer periods occurring within those years.

## Spatial Support

Spatial support identifies the geographic area represented by an aggregate.

For the New Hampshire example, the spatial coverage is the state of **New Hampshire**, represented using its geographic boundary and polygon geometry.

The spatial support describes the extent over which the aggregate was calculated and is distinct from the spatial resolution of the source data. In this example, measurements from the two SCAN sites are combined to produce an aggregate representing the specified New Hampshire geographic extent.

## Base Dataset and Resolution

Each aggregate is linked to the dataset from which it was calculated. For example, `ex:DailyMeanTempDataset_1` identifies the underlying daily temperature dataset used in the calculation.

The associated dataset description, `ex:NH_DatasetDescription`, characterizes the spatial and temporal resolution of the source data.

Representing both the base dataset and its resolution allows STAD to distinguish between:

- the **resolution of the source observations**, and
- the **spatial and temporal support of the resulting aggregate**.

This distinction is important when interpreting or comparing aggregate data. Two aggregates may cover the same geographic area and time period while being derived from source datasets with different spatial or temporal resolutions.

## Statistical Aggregation and Provenance

STAD explicitly represents the statistical transformation used to generate an aggregate.

For the New Hampshire summer mean temperature example, the statistical transformation is represented by `ex:ArithmeticMeanExecution`, an instance of `stad-mls:AlgorithmExecution`. This execution realizes a `stad:ArithmeticMeanCalculation` and records the parameter settings associated with the calculation.

Representing the aggregation process explicitly provides provenance for the resulting value. It allows users to distinguish aggregates that may have the same spatial and temporal support but were generated using different algorithms, parameter settings, or source data.

For example, two temperature aggregates may cover the same geographic region and time period but produce different values if one is calculated from all available hourly observations while another is calculated from only daily minimum and maximum temperatures. STAD provides the semantic structure needed to represent these differences explicitly.

## SPARQL Evaluation

The instantiated STAD knowledge graph is used to evaluate the competency questions defined for the ontology.

Each competency question is represented as a SPARQL query that retrieves or compares aggregate data based on its semantic properties. The queries evaluate STAD's ability to support questions involving:

- spatial and temporal coverage;
- spatial and temporal resolution;
- temporal aggregation periods;
- aggregation methods;
- source datasets;
- algorithm executions and provenance;
- aggregate quantities and values; and
- comparisons between aggregates with different spatial, temporal, or computational characteristics.

The SPARQL evaluation demonstrates that STAD supports more than simple retrieval of numerical values. Because the semantics of each aggregate are explicitly represented, queries can identify and compare aggregates based on **what was aggregated, where it was aggregated, when it was aggregated, the resolution and support of the source data, and how the aggregate was calculated**.
